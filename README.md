
import random
import math
import time
import the
import pygame




ausgewaehltes_spaceship = 1
class Game:
    def __init__(self, whidth, high):
        pygame.init()
        self.whidth = whidth
        self.high = high
        self.screen = pygame.display.set_mode((self.whidth, self.high))
        pygame.display.set_caption("Space Invaders")
        self.clock = pygame.time.Clock()
        self.running = True
        self.spaceship = Spaceship(self, 370, 515)
        self.ausgewaehltes_spaceship = 1
        self.score = 0
        self.bosstp = 0
        self.bossscore = 0
        self.stopschleife = False
        shop_to_4_shop = False
        self.hg_page = 1
        self.new_game = False
        self.png_start_menue = pygame.image.load("hauptmenue.png")  # 1
        self.background_ing = pygame.image.load("stars.png")  # 2
        self.png_stats = pygame.image.load("Stats_1.png")  # 3
        self.png_shop_1 = pygame.image.load("png_shop_1_gekauft.png")  # 4
        self.png_shop_2 = pygame.image.load("png_shop_2.png")  # 5
        self.png_shop_3 = pygame.image.load("png_shop_3.png")  # 6
        self.einlog_running = True
        self.hauptmenue_running = True
        self.stat_score = 0
        self.new_stat_score = 0
        self.stat_highscore = 0
        self.start_einlog = True
        self.buyed_spaceship_list = []
        self.spaceship_2_gekauft = False
        self.spaceship_3_gekauft = False
        self.damage = 1
        self.dont_move = False
        self.count = 0
        self.paused = False
        self.coins = 0




        self.name_reader()
        self.enemys = []
        for i in range(12):
            self.enemys.append(Enemy(self, random.randint(0, 736), random.randint(30, 130)))

        self.shootenemys = []
        for i in range(1):
            self.shootenemys.append(Shootenemy(self, 300, 50))

        # self.background_ing = pygame.image.load("stars.png")

        while self.running:
            self.clock.tick(60)
            self.hautmenue_schleife()
            self.death_waiter()
            self.screen.blit(self.background_ing, (0, 0))
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    self.running = False
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        self.spaceship.fire_laser()
                    if event.key == pygame.K_ESCAPE:
                        self.paused = True
                        self.pause_menue()

                    if event.key == pygame.K_LEFT:
                        self.spaceship.move(-10)

                    if event.key == pygame.K_RIGHT:
                        self.spaceship.move(10)

                    if event.key == pygame.K_SPACE:
                        self.spaceship.fire_bullet()

                if event.type == pygame.KEYUP:
                    if event.key == pygame.K_LEFT:
                        self.spaceship.move(10)
                    if event.key == pygame.K_RIGHT:
                        self.spaceship.move(-10)




                if event.type == pygame.MOUSEBUTTONDOWN:
                    # if event.type == pygame.mouse.get_pressed() == (True, False, False):
                    print(pygame.mouse.get_pos())
                    self.hitbox_check()


            if len(self.spaceship.lasers) > 0:
                for laser in self.spaceship.lasers:
                    if laser.is_fired == True:
                        laser.update()
                    else:
                        self.spaceship.lasers.remove(laser)


            self.spaceship.update()
            if len(self.spaceship.bullets) > 0:
                for bullet in self.spaceship.bullets:
                    if bullet.is_fired == True:
                        bullet.update()
                    else:
                        self.spaceship.bullets.remove(bullet)

            for enemy in self.enemys:
                enemy.update(self.dont_move)
                enemy.check_colision()
                if enemy.y > 500:
                    for i in self.enemys:
                        i.y = 1000
                    self.print_game_over()
                    break

            for shootenemy in self.shootenemys:
                shootenemy.update()
                shootenemy.checkcoliesion()

            self.print_score()
            pygame.display.update()




    def one_more_coin(self):
        self.coins += 1

    def read_stats(self):
        self.highscore = the.stats(str(self.name), "2")
        self.buyed_spaceship_list = the.stats(str(self.name), "3")
        self.coins = the.stats(str(self.name), "4")






    def space_ship_changer(self, x):

        if x == 1:
            self.spaceship.spaceship_ing = self.spaceship.spaceship_ing_1


        if x == 2:
            self.spaceship.spaceship_ing = self.spaceship.spaceship_ing_2

        if x == 3:
            self.spaceship.spaceship_ing = self.spaceship.spaceship_ing_3

    def stats_writer(self):
        stats_txt = open("Stats.txt", "r")
        for line in stats_txt:
            pass
            #line[:20]

    def name_reader(self):
        acount_dat = open("savename.txt", "r")
        self.name = acount_dat.read()
        acount_dat.close()




    def hautmenue_schleife(self):
        while self.hauptmenue_running:
            self.blit_hg()
            self.clock.tick(60)
            self.write_name()
            self.gekauft_oder_nicht()
            self.shop_to_4_shop = False
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    self.hauptmenue_running = False
                    self.running = False
                    break
                if event.type == pygame.KEYDOWN:
                    pass

                if event.type == pygame.MOUSEBUTTONDOWN:
                    # if event.type == pygame.mouse.get_pressed() == (True, False, False):
                    print(pygame.mouse.get_pos())
                    self.hitbox_check()
                    print(self.hg_page)


            pygame.display.update()

    def gekauft_oder_nicht(self):
        if self.spaceship_2_gekauft == True:
            self.png_shop_2 = pygame.image.load("png_shop_2_gekauft.png")

        if self.spaceship_3_gekauft == True:
            self.png_shop_3 = pygame.image.load("png_shop_3_gekauft.png")

    def write_name(self):
        pygame.font.init()
        if self.hg_page == 1:
            name_font = pygame.font.Font("freesansbold.ttf", 60)
            name_text = name_font.render(self.name, True, (0, 0, 0))
            self.screen.blit(name_text, (30, 30))

    def blit_hg(self):
        if self.hg_page == 1:
            self.screen.blit(self.png_start_menue, (0, 0))

        if self.hg_page == 3:
            self.screen.blit(self.png_stats, (0, 0))

        if self.hg_page == 2:
            self.screen.blit(self.background_ing, (0, 0))

        if self.hg_page == 4:
            self.screen.blit(self.png_shop_1, (0, 0))

        if self.hg_page == 5:
            self.screen.blit(self.png_shop_2, (0, 0))

        if self.hg_page == 6:
            self.screen.blit(self.png_shop_3, (0, 0))

    def hitbox_check(self):
        if self.hg_page == 1:
            self.mouse_position = pygame.mouse.get_pos()
            if self.mouse_position[0] >= 275 and self.mouse_position[0] <= 485:
                if self.mouse_position[1] >= 470 and self.mouse_position[1] <= 540:
                    self.hauptmenue_running = False
                    self.hg_page = 2

            if self.mouse_position[0] >= 555 and self.mouse_position[0] <= 767:
                if self.mouse_position[1] >= 154 and self.mouse_position[1] <= 224:
                    self.hg_page = 3

            if self.mouse_position[0] >= 25 and self.mouse_position[0] <= 183:
                if self.mouse_position[1] >= 166 and self.mouse_position[1] <= 250:
                    self.hg_page = 4
        if self.hg_page == 2:
            pass

        if self.hg_page == 3:
            self.mouse_position = pygame.mouse.get_pos()
            if self.mouse_position[0] >= 66 and self.mouse_position[0] <= 279:
                if self.mouse_position[1] >= 59 and self.mouse_position[1] <= 128:
                    self.hg_page = 1

        if self.hg_page == 4:
            self.mouse_position = pygame.mouse.get_pos()
            if self.mouse_position[0] >= 66 and self.mouse_position[0] <= 279:
                if self.mouse_position[1] >= 59 and self.mouse_position[1] <= 128:
                    self.hg_page = 1

            if self.mouse_position[0] >= 52 and self.mouse_position[0] <= 266:
                if self.mouse_position[1] >= 169 and self.mouse_position[1] <= 240:
                    if self.hg_page != 4:
                        self.hg_page -= 1

            if self.mouse_position[0] >= 266 and self.mouse_position[0] <= 477:
                if self.mouse_position[1] >= 494 and self.mouse_position[1] <= 564:
                    if 1 != 2:
                        self.spaceship.change_x_negative = -10
                        self.spaceship.change_x_negative = 10
                        self.change_x_positiv = 10
                        self.space_ship_changer(1)
                        print(1)

            if self.mouse_position[0] >= 574 and self.mouse_position[0] <= 787:
                if self.mouse_position[1] >= 49 and self.mouse_position[1] <= 117:
                    if self.shop_to_4_shop == False:
                        self.hg_page = 5
                        self.shop_to_4_shop = True


            if self.mouse_position[0] >= 582 and self.mouse_position[0] <= 792:
                if self.mouse_position[1] >= 200 and self.mouse_position[1] <= 423:
                    print("Statd:" + str(self.hg_page))

        if self.hg_page == 5:
            self.mouse_position = pygame.mouse.get_pos()
            if self.mouse_position[0] >= 66 and self.mouse_position[0] <= 279:
                if self.mouse_position[1] >= 59 and self.mouse_position[1] <= 128:
                    self.hg_page = 1

            if self.mouse_position[0] >= 582 and self.mouse_position[0] <= 792:
                if self.mouse_position[1] >= 200 and self.mouse_position[1] <= 423:
                    print("Statd:" + str(self.hg_page))



            if self.mouse_position[0] >= 52 and self.mouse_position[0] <= 266:
                if self.mouse_position[1] >= 169 and self.mouse_position[1] <= 240:
                    if self.hg_page != 4:
                        self.hg_page = 4

            if self.mouse_position[0] >= 574 and self.mouse_position[0] <= 787:
                if self.mouse_position[1] >= 49 and self.mouse_position[1] <= 117:
                    if self.shop_to_4_shop == False:
                        self.hg_page = 6
                        self.shop_to_4_shop = True

            if self.mouse_position[0] >= 266 and self.mouse_position[0] <= 477:
                if self.mouse_position[1] >= 494 and self.mouse_position[1] <= 564:
                    if self.spaceship_2_gekauft == True:
                        self.spaceship.change_x_negative = -15
                        self.spaceship.change_x_negative = 15
                        self.space_ship_changer(2)
                        self.damage = 3
                    else:
                        print("kaufen")
                        self.spaceship_2_gekauft = True

        if self.hg_page == 6:
            self.mouse_position = pygame.mouse.get_pos()
            if self.mouse_position[0] >= 66 and self.mouse_position[0] <= 279:
                if self.mouse_position[1] >= 59 and self.mouse_position[1] <= 128:
                    self.hg_page = 1


            if self.mouse_position[0] >= 582 and self.mouse_position[0] <= 792:
                if self.mouse_position[1] >= 200 and self.mouse_position[1] <= 423:
                    print("zwei mehr infos")



            if self.mouse_position[0] >= 52 and self.mouse_position[0] <= 266:
                  if self.mouse_position[1] >= 169 and self.mouse_position[1] <= 240:
                    if self.hg_page != 4:
                        self.hg_page = 5

            if self.mouse_position[0] < 575 or self.mouse_position[0] > 787:
                if self.mouse_position[1] >= 49 and self.mouse_position[1] <= 117:
                    self.hg_page += 0

            if self.mouse_position[0] >= 266 and self.mouse_position[0] <= 477:
                if self.mouse_position[1] >= 494 and self.mouse_position[1] <= 564:
                    if self.spaceship_3_gekauft == True:
                        self.spaceship.change_x_negative = -15
                        self.spaceship.change_x_negative = 15
                        self.space_ship_changer(3)
                        self.damage = 2
                    else:
                        self.spaceship_3_gekauft = True

    def print_game_over(self):
        go_font = pygame.font.Font("freesansbold.ttf", 64)
        go_text = go_font.render("GAME OVER", True, (255, 255, 255))
        self.screen.blit(go_text, (200, 250))
        self.new_game = True



    def death_waiter(self):
        if self.new_game == True:
            self.count += 1
            if self.count >= 3:
                for bullet in self.spaceship.bullets:
                    bullet.is_fired = False
                time.sleep(1.5)
                self.hauptmenue_running = True
                self.hg_page = 1
                self.new_game = False


    def print_score(self):
        score_font = pygame.font.Font("freesansbold.ttf", 24)
        score_text = score_font.render("Punke:" + str(self.score), True, (255, 255, 255))
        self.screen.blit(score_text, (8, 8))





    def pause_menue(self):
        alien_img = pygame.image.load("Alienspaceship.png")
        while self.paused == True:
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    self.paused = False
                    self.running = False
                    self.hauptmenue_running = False
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_ESCAPE:
                        self.paused = False

                    if event.key == pygame.K_NUMLOCK:
                        self.score += 50






class Spaceship:
    def __init__(self, game, x, y):
        self.first_spaceship = False
        self.x = x
        self.y = y
        self.change_x = 0
        self.change_x_negative = -10
        self.change_x_positiv = 10
        self.game = game
        self.bullets = []
        self.lasers = []
        self.spaceship_ing_1 = pygame.image.load("b7ae33c0-b051-11ed-9f44-710cf58997ba.png")
        self.spaceship_ing_2 = pygame.image.load("Zeichnung 1 (3).png")
        self.spaceship_ing_3 = pygame.image.load("Zeichnung 8.png")
        if self.first_spaceship == False:
            self.spaceship_ing = self.spaceship_ing_1
            self.first_spaceship = True


    def move(self, speed):
        if self.spaceship_ing == self.spaceship_ing_1:
            if speed > 0:
                self.change_x += 10
            else:
                self.change_x -= 10

        if self.spaceship_ing == self.spaceship_ing_2:
            if speed > 0:
                self.change_x += 15
            else:
                self.change_x -= 50

        if self.spaceship_ing == self.spaceship_ing_3:
            if speed > 0:
                self.change_x += 15
            else:
               self.change_x -= 15





    def fire_bullet(self):
        self.bullets.append(Bullet(self.game, self.x, self.y))
        self.bullets[len(self.bullets) - 1].fire()

    def fire_laser(self):
        self.lasers.append(Laser(self.game, self.x, self.y))
        self.lasers[len(self.lasers) - 1].fire()




    def update(self):

        self.x += self.change_x
        if self.x < 0:
            self.x = 0
        elif self.x > 736:
            self.x = 736


        self.game.screen.blit(self.spaceship_ing, (self.x, self.y))






class Bullet:
    def __init__(self, game, x, y):
        self.x = x
        self.y = y
        self.game = game
        self.is_fired = False
        self.bullet_speed = 5
        self.bullet_ing = pygame.image.load("Bullet.png")

    def fire(self):
        self.is_fired = True


    def update(self):
        self.y -= self.bullet_speed
        if self.y <= 0:
            self.is_fired = False

        self.game.screen.blit(self.bullet_ing, (self.x, self.y))



class Laser:
    def __init__(self, x, y, game):
        self.x = x
        self.y = y
        self.game = game
        self.laser_picture = pygame.image.load("Bullet.png")
        self.is_fired = False
        self.laser_speed = 20
        self.button_down = False

    def fire(self):
        self.is_fired = True

    def update(self):
        self.game.screen.blit(self.laser_picture, (self.x, self.y))







class Enemy:
    def __init__(self, game, x, y):
        self.game = game
        self.x = x
        self.y = y
        self.change_x = 15
        self.change_y = 50
        self.enemy_ing = pygame.image.load("Alienspaceship.png")



    def only_update(self):
        # Zugriff auf das Attribut game des Enemy-Objekts
        self.game.screen.blit(self.enemy_ing, (self.x, self.y))

    def check_colision(self):
        for bullet in self.game.spaceship.bullets:
            distance = math.sqrt(math.pow(self.x - bullet.x, 2) + math.pow(self.y - bullet.y, 2))
            if distance < 35:
                bullet.is_fired = False
                self.game.score += 1
                self.game.bossscore += 1
                #self.game.coins += 1
                self.x = random.randint(0, 736)
                self.y = random.randint(50, 150)
                # stats
                if self.game.stat_score == 0:
                    self.game.stat_score += 1






    def update(self, x):
        if x == False:
            self.x += self.change_x
            if self.x >= 736:
                self.y += self.change_y
                self.change_x = -5
            elif self.x <= 0:
                self.y += self.change_y
                self.change_x = 5

        self.game.screen.blit(self.enemy_ing, (self.x, self.y))







class Shootenemy:
    def __init__(self, game, x, y):
        self.x = x
        self.y = y
        self.shoot_enemy_ing = pygame.image.load("Shootenemy.png")
        self.game = game
        self.change_x = 10
        self.lives = 15

    def fire_bullet(self):
        Enemybullet(self.game, self.x, self.y)
        self.Bullets = []

    def update(self):

        if self.game.bosstp == 0:
            self.x = 10000
            self.game.bosstp = 1

        if self.game.bossscore >= 50:
            self.x = 300
            self.game.bossscore -= 50

        self.game.screen.blit(self.shoot_enemy_ing, (self.x, self.y))

    def checkcoliesion(self):
        for bullet in self.game.spaceship.bullets:
            if self.x == 300:
            #  if 0 == 0:

                if bullet.x >= 300 and bullet.x <= 525 and bullet.y >= 70 and bullet.y <= 250:# and self.game.bosstp == 0:
                    bullet.is_fired = False
                    #self.lives -= x
                    self.game.bosstp = 0

    def live_counter(self):
        self.lives -= game.damage
        if self.lives <= 0:
            self.game.bosstp = 0
            self.lives = 15






class Enemybullet:
    def __init__(self, game, x, y):
        self.x = x
        self.y = y
        self.game = game
        self.is_fired = False
        self.bullet_speed = 5
        self.bullet_ing = pygame.image.load("Bullet.png")

    def fire(self):
        self.is_fired = True

    def update(self):
        self.y -= self.bullet_speed
        if self.y <= 0:
            self.is_fired = False
        self.game.screen.blit(self.bullet_ing, (self.x, self.y))


if __name__ == "__main__":
    game = Game(800, 600)
