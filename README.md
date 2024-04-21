# gameplay
from pygame import *
from random import randint

class GameSprite(sprite.Sprite):
    def __init__(self, player_image, x, y, speed):
        super().__init__()
        self.image = transform.scale(image.load(player_image), (65, 65))
        self.speed = speed
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))

class Player(GameSprite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < win_width - 80:
            self.rect.x += self.speed
        
    def fite(self):
        bullet = Bullet('bullet.png', self.rect.centerx, self.rect.top, -15) 
        bullets.add(bullet)

class Enemy(GameSprite):
    def update(self):        
        self.rect.y += self.speed
        global lost
        if self.rect.y > 450:
            self.rect.x = randint(80, 620)
            self.rect.y = 0 
            lost = lost + 1

class Bullet(GameSprite):
    def update(self):
        self.rect.y += self.speed
        if self.rect.y < 0:
            self.kill()

win_width = 700
win_height = 500

window = display.set_mode((win_width, win_height))
display.set_caption('')              

background = transform.scale(image.load('galaxy.jpg'), (win_width, win_height))

font.init()
font1 = font.Font(None, 36)
font2 = font.Font(None, 60)

bullets = sprite.Group()

mixer.init()
mixer.music.load('space.ogg')
mixer.music.play()


game = True

clock = time.Clock()
FPS = 60

 
ufo_1 = Enemy('ufo.png', randint(80, 620),0,randint(1, 5)) 
ufo_2 = Enemy('ufo.png', randint(80, 620),0,randint(1, 5))
ufo_3 = Enemy('ufo.png', randint(80, 620),0,randint(1, 5))
ufo_4 = Enemy('ufo.png', randint(80, 620),0,randint(1, 5))
ufo_5 = Enemy('ufo.png', randint(80, 620),0,randint(1, 5))

monsters = sprite.Group()
monsters.add(ufo_1)
monsters.add(ufo_2)
monsters.add(ufo_3)
monsters.add(ufo_4)
monsters.add(ufo_5)

rocket = Player('rocket.png',300,420,4)

finish = False
game = True

score = 0
lost = 0

while game:
    for e in event.get():
        if e.type == QUIT:
            game = False 
        if e.type == KEYDOWN:
            if e.key == K_SPACE:
                rocket.fite()
    if finish != True:
        text_lose = font1.render('пропущено: ' + str(lost),1,(255, 255, 255))
        text_score = font1.render('Счет: ' + str(score),1,(255, 255, 255))
        window.blit(background, (0, 0))
        window.blit(text_lose, (20,20))
        window.blit(text_score, (20,5))

        rocket.update()
        rocket.reset()
        monsters.update()
        monsters.draw(window)
        bullets.update()
        bullets.draw(window)
        sprites_list = sprite.groupcollide(monsters, bullets, True, True)
        for el in sprites_list:
            score += 1
            monster = Enemy('ufo.png', randint(80, 620),0,randint(1, 5))
            monsters.add(monster) 
        if score > 10:
            text_win = font2.render('YOU WON!', 1, (0, 255, 0))
            window.blit(text_win, (250, 200))
            finish = True
        if lost > 3:
            end = font2.render('YOU LOSE!', 1, (255, 0, 0))
            window.blit(end, (250, 200))
            finish = True
    
    
    display.update()
    clock.tick(FPS)
