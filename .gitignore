from time import time as millis
import time
from pygame import *
import random
width = 700
height = 500
window = display.set_mode((width, height))
display.set_caption('шутер')

clock = time.Clock()
FPS = 60


class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, player_speed, width = 65, height = 65):
        super().__init__()
        self.speed = player_speed 
        self.image = transform.scale(image.load(player_image), (width, height))
        self.rect = self.image.get_rect()
        self.rect.y = player_y
        self.rect.x = player_x
    
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y)) 


class Player(GameSprite):
    prev_shot = millis()
    rel_flag = False
    num_fire = 0
    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < 630:
            self.rect.x += self.speed
        if keys[K_SPACE]:
            self.fire() 
    def fire(self):
        if self.rel_flag and millis() - self.prev_shot < 3:
            return
        if self.rel_flag and millis() - self.prev_shot > 3:
            self.rel_flag = False
            self.num_fire = 0
        if millis() - self.prev_shot > 0.2:
            fire_sound.play()
            x = self.rect.centerx
            y = self.rect.y
            bullet = Bullet('bullet.png', x, y, 10, 10, 20)
            bullet.rect.x -= bullet.rect.width // 2
            bullets.add(bullet)
            self.prev_shot = millis()
            self.num_fire += 1
            if self.num_fire >= 5:
                self.rel_flag = True

        
class Enemy(GameSprite):
    def update(self):
        global lost
        self.rect.y += self.speed
        if self.rect.y > height:
            self.rect.y = -100
            self.rect.x = random.randint(0, width - self.rect.width)
            lost += 1
            
class Bullet(GameSprite):
    def update(self):
        self.rect.y -= self.speed
        if self.rect.y < self.rect.height:
            self.kill()

class Obstacle(GameSprite):
    def update(self):
        self.rect.y += self.speed
        if self.rect.y > height:
            self.rect.y = -100
            self.rect.x = random.randint(0, width - self.rect.width)
            self.speed = random.randint(1, 5)


font.init()
font = font.Font(None, 40)

monsters = sprite.Group() 
for i in range(5):
    x = random.randint(0, width - 80)
    speed = random.randint(2, 5)
    monster = Enemy('asteroid.png', x, -100, speed) 
    monsters.add(monster)

obstacles = sprite.Group()
for i in range(3):
    x = random.randint(0, width - 80)
    speed = random.randint(1, 5)
    obstacle = Obstacle('ufo.png', x, -100, speed)
    obstacles.add(obstacle)


rocket = Player('rocket.png', 350, 430, 8)


background = transform.scale(image.load('galaxy.jpg'), (700, 500))
player = Player('rocket.png', (width - 65) // 2, height - 70,10)
bullets = sprite.Group()
 

finish = False
run = True
lost = 0   
score = 0

win = font.render(
    'YOU WIN', True, (0,255,0)
)

lose = font.render(
    'YOU LOSE', True, (255, 0, 0)
)


mixer.init()
mixer.music.load('space.ogg')
mixer.music.play()
fire_sound = mixer.Sound('fire.ogg') 

while run:
    window.blit(background,(0, 0))
    for e in event.get():   
        if e.type == QUIT:
            run = False
    
    hits = sprite.groupcollide(monsters, bullets, True, True)
    for hit in hits:
        x = random.randint(0, width - 80)
        speed = random.randint(2, 5)
        monster = Enemy('asteroid.png', x, -100, speed) 
        monsters.add(monster)
        score += 1

    for enemy in monsters:
            gets = sprite.spritecollideany(rocket, monsters)

    if not finish:
        window.blit(background,(0, 0))

        lost_text = font.render(
            'Пропущено: ' + str(lost), True, (255,69,0)
        )

        score_text = font.render(
            'Счёт: ' + str(score), True, (0,250,154)
        )  

        if len(sprite.spritecollide(rocket, obstacles, True)):
            x = random.randint(0, width - 80)
            speed = random.randint(1, 5)
            obstacle = Obstacle('ufo.png', x, -100, speed)
            obstacles.add(obstacle)

        window.blit(score_text, (10, 10))
        window.blit(lost_text, (10, 50))
        rocket.update()
        rocket.reset()
        monsters.update()
        monsters.draw(window)
        bullets.update()
        bullets.draw(window)
        obstacles.update()
        obstacles.draw(window)
        if lost >= 5 or gets:
            result = lose
            finish = True
        if score >= 50:
            result = win
            finish = True
        last_time = millis()
    else:
        window.blit(background, (0, 0))
        window.blit(result, (280, 250))
        if millis() - last_time > 5:
            score = 0
            lost = 0
            bg_rect = background.get_rect()
            player.rect.centerx = bg_rect.centerx
            monsters.empty()
            for i in range(5):
                x = random.randint(0, width - 80)
                speed = random.randint(1, 8)
                monster = Enemy('asteroid.png', x, -100, speed)
                monsters.add(monster)
            bullets.empty()
            finish = False
    display.update()
    clock.tick(FPS) 
