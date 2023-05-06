import random

game_over = False

time_remaining = 15

NUM_ROWS = 3
NUM_COLS = 3

RESOLUTION_W = 360
RESOLUTION_H = 600
x_position = [i for i in range(NUM_ROWS)]
y_position = [i for i in range(NUM_COLS)]
cnt = 0

class Platform:
    def __init__(self,x,y,w,h):
        self.x = x
        self.y = y
        self.w = w
        self.h = h
        
    def display(self):
        fill(0,125,0)
        rect(self.x, self.y,self.w,self.h)
        
platforms = []

for i in x_position:
    for j in y_position:
        platforms.append(Platform(25+i*120,150+j*160,70,70))
        
        
# Class for normal moles with 2 sizes. When being hit, the small-sized will give 2 scores, while the big-sized will give 1 score.
class Mole:
    def __init__(self):
        m = random.choice(platforms)
        self.platform = m
        self.x = self.platform.x + self.platform.w / 2
        self.y = self.platform.y + self.platform.h / 2
        self.r = random.choice([25,35])
        self.value = 0

                
        #Set score values for the moles based on their sizes
        if self.r == 25:
            self.value = 2
        elif self.r == 35:
            self.value = 1
            
        self.vy = 5
        self.is_moving_up = True
        self.is_clicked = False
    
    def update(self):
        if self.is_moving_up:
            self.y -= 5  # move the mole up by 5 pixels
            if self.y + self.r <  self.platform.y:
                self.is_moving_up = False  # switch to moving down when the mole reaches the top
        else:
            self.y += 5  # move the mole down by 5 pixels
            if self.y + self.r >= self.platform.y + self.r:  # check if the mole has reached its maximum down position
                self.y = self.platform.y + self.r 
                game.mole = Mole()
                while game.mole.platform == game.special_mole.platform:
                    print("collision")
                    game.mole = Mole()

        if self.is_clicked:
            self.y = self.platform.y + self.r 
            game.mole = Mole()
            print("COUNT:", game.cnt)
            while game.mole.platform == game.special_mole.platform:
                print("collision")
                game.mole = Mole()       
                
    def display(self):
        strokeWeight(0)
        fill(255,0,0)
        ellipse(self.x, self.y, self.r * 2, self.r * 2)
        
# Create a class for special moles that will reduce 5 scores when the player hits them.
class SpecialMole(Mole):
    
    def __init__(self):
        n = random.choice(platforms)
        self.platform = n
        self.x = self.platform.x + self.platform.w / 2
        self.y = self.platform.y + self.platform.h / 2
        self.r = 35
        self.vy = 5
        self.value = -5 
        self.is_moving_up = True
        self.is_clicked = False
        
        # self.lottery_clicked = False
        
    def update(self):
        if self.is_moving_up:
            self.y -= 5  # move the mole up by 5 pixels
            if self.y + self.r <  self.platform.y:
                self.is_moving_up = False  # switch to moving down when the mole reaches the top
        else:
            self.y += 5  # move the mole down by 5 pixels
            if self.y + self.r >= self.platform.y + self.r:  # check if the mole has reached its maximum down position
                self.y = self.platform.y + self.r 
                game.special_mole = SpecialMole()
                while game.mole.platform == game.special_mole.platform:
                    print("collision")
                    game.special_mole = SpecialMole()
        

        if self.is_clicked:
            self.y = self.platform.y + self.r 
            game.special_mole = SpecialMole()
            while game.mole.platform == game.special_mole.platform:
                print("collision")
                game.special_mole = SpecialMole()
            
    def display(self):
        strokeWeight(0)
        fill(0,0,255)
        ellipse(self.x, self.y, self.r * 2, self.r * 2)

# class Lottery:
#     def __init__(self):
#         self.mole_list = []
#         self.mole = Mole()

#     def add_mole(self):
#         for platform in platforms:
#             self.mole_list.append(mole)
        
#     def display(self):
#         strokeWeight(0)
#         fill(255,0,0)
#         ellipse(self.mole.x, self.mole.y, self.mole.r * 2, self.mole.r * 2)
#         print("Lottery")
        
class Game:
    def __init__(self):
        self.time = time_remaining
        self.speed = 5
        self.w = RESOLUTION_W
        self.h = RESOLUTION_H
        self.score = 0
        self.cnt = cnt

        self.platforms = []
        
        for i in x_position:
            for j in y_position:
                self.platforms.append(Platform(25+i*120,150+j*160,70,70))
        
        self.lottery = []
        for i in x_position:
            for j in y_position:
                mole_in_list = Mole()
                self.lottery.append(mole_in_list)
        
        self.mole = Mole()
        self.special_mole = SpecialMole()
        #Make sure that position of the normal mole and special mole is not the same
        while self.mole.platform == self.special_mole.platform:
            print("collision")
            self.special_mole = SpecialMole()
         
    def update(self):
        game.mole.update()
        game.special_mole.update()

        if frameCount%60 == 0:
            self.time -= 1
              
    def display(self):            
        self.mole.display()
        self.special_mole.display()
            
        for platform in self.platforms:
            platform.display()
            
        # if self.cnt %10 == 0:
        #     for mole_in_list in self.lottery:
        #         mole_in_list.display()
        #         print("LOTTERY HERE")
            
    
    def mousePressed(self):
            if (self.mole.x - self.mole.r <= mouseX <= self.mole.x + self.mole.r
                and self.mole.y - self.mole.r <= mouseY <= self.mole.y + self.mole.r):
                # if self.mole.y - self.mole.platform.y > self.mole.r:
                self.mole.is_clicked = True
                print("You click the mole")
                self.cnt += 1
                self.score += self.mole.value
            
            if (self.special_mole.x - self.special_mole.r <= mouseX <= self.special_mole.x + self.special_mole.r
                and self.special_mole.y - self.special_mole.r <= mouseY <= self.special_mole.y + self.special_mole.r):
                # if self.mole.y - self.mole.platform.y > self.mole.r:
                self.special_mole.is_clicked = True
                print("You click the SPECIAL mole")
                self.score += self.special_mole.value
            
    def check_timing(self):
        if self.time == 0:
            game_over == True
                            
def setup():
    size(RESOLUTION_W, RESOLUTION_H)
    background(255,255,255)
    # text("Time remaining: {}".format(time_remaining),200, 10)    

def draw():
    if frameCount%(max(1, int(8 - game.speed)))==0 or frameCount==1:
        background(210)
        game.update()
        game.display()
        
        fill(255)
        textSize(20)
        textAlign(RIGHT, TOP)
        text("Score: " + str(game.score), width-10, 10)

        minute = game.time//60
        second = game.time%60
        fill(255)
        textSize(24)
        textAlign(LEFT, TOP)
        text("{0}:{1}".format(minute,second), 10, 10)     
        
    if game_over == True:
        fill(255)
        textSize(40)
        # textAlign(RIGHT, TOP)
        text("Gameover!", 60, RESOLUTION_H/2)
      
def mousePressed():
    game.mousePressed()
    
game = Game()
