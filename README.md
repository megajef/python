# python
# Implementation of classic arcade game Pong

import simplegui
import random

# initialize globals - pos and vel encode vertical info for paddles
WIDTH = 600
HEIGHT = 400       
BALL_RADIUS = 20
PAD_WIDTH = 8
PAD_HEIGHT = 80
HALF_PAD_WIDTH = PAD_WIDTH / 2
HALF_PAD_HEIGHT = PAD_HEIGHT / 2
LEFT = False
RIGHT = True
ball_pos = [0,0]
ball_vel = [0,0]
paddle1_pos = 0.0
paddle2_pos = 0.0
paddle1_vel = 0.0
paddle2_vel = 0.0
time = .01
score1 = 0
score1 = 0

# initialize ball_pos and ball_vel for new bal in middle of table
# if direction is RIGHT, the ball's velocity is upper right, else upper left
def spawn_ball(direction):
    global ball_pos, ball_vel # these are vectors stored as lists
    ball_pos = [WIDTH / 2, HEIGHT / 2]
    if(direction == RIGHT):
        ball_vel = [random.randrange(120, 240),-random.randrange(60, 180)]
    elif(direction == LEFT):
        ball_vel = [-random.randrange(120, 240), -random.randrange(60, 180)] 

# define event handlers
def new_game():
    global paddle1_pos, paddle2_pos, paddle1_vel, paddle2_vel  # these are numbers
    global score1, score2  # these are ints
    
    ball_dir = random.randint(0,1)
    if(ball_dir == 0):
        spawn_ball(RIGHT)
    else:
        spawn_ball(LEFT)
    paddle1_pos = HEIGHT / 2
    paddle2_pos = HEIGHT / 2
    paddle1_vel = 0
    paddle2_vel = 0
    score1 = 0
    score2 = 0
        
def tick():
    pass

def draw(canvas):
    global score1, score2, time, paddle1_pos, paddle2_pos, ball_pos, ball_vel
 
    # draw mid line and gutters
    canvas.draw_line([WIDTH / 2, 0],[WIDTH / 2, HEIGHT], 1, "White")
    canvas.draw_line([PAD_WIDTH, 0],[PAD_WIDTH, HEIGHT], 1, "White")
    canvas.draw_line([WIDTH - PAD_WIDTH, 0],[WIDTH - PAD_WIDTH, HEIGHT], 1, "White")
          
    # Reflection when ball bounces off top
    if(ball_pos[1] <= BALL_RADIUS):
        ball_vel[1] = -ball_vel[1]
    # Reflection when ball bounces off bottom
    if(ball_pos[1] >= (HEIGHT-1) - BALL_RADIUS):
        ball_vel[1] = -ball_vel[1]
        
    #Restart if ball touches the left gutters 
    if(ball_pos[0] - BALL_RADIUS <= PAD_WIDTH):
        if((ball_pos[1] >= paddle1_pos - HALF_PAD_HEIGHT) and (ball_pos[1] <= paddle1_pos + HALF_PAD_HEIGHT)):
            ball_vel[0] = -ball_vel[0]
        elif(ball_pos[0] < PAD_WIDTH + BALL_RADIUS):
            ball_pos = [WIDTH / 2, HEIGHT /2]
            score1 += 1
            spawn_ball(RIGHT)
    
    #Restart if ball touches the right gutters
    if(ball_pos[0] + BALL_RADIUS >= WIDTH - PAD_WIDTH):
        if((ball_pos[1] >= paddle2_pos - HALF_PAD_HEIGHT) and (ball_pos[1] <= paddle2_pos + HALF_PAD_HEIGHT)):
            ball_vel[0] = -ball_vel[0]
        elif(ball_pos[0] > PAD_WIDTH + BALL_RADIUS):
            ball_pos = [WIDTH / 2, HEIGHT /2]
            score2 += 1
            spawn_ball(LEFT)
         
    # update ball    
    ball_pos[0] = ball_pos[0] +  time * ball_vel[0]
    ball_pos[1] = ball_pos[1] + time * ball_vel[1]
    
    # draw ball
    canvas.draw_circle(ball_pos, BALL_RADIUS, 2, "White", "White")
    
    # update paddle's vertical position, keep paddle on the screen 
    if(paddle1_pos - HALF_PAD_HEIGHT + paddle1_vel > 0 and paddle1_pos + HALF_PAD_HEIGHT + paddle1_vel < HEIGHT):
         paddle1_pos += paddle1_vel
    if(paddle2_pos - HALF_PAD_HEIGHT + paddle2_vel > 0 and paddle2_pos + HALF_PAD_HEIGHT + paddle2_vel < HEIGHT):
         paddle2_pos += paddle2_vel
           
    # draw paddles
    canvas.draw_line((PAD_WIDTH - HALF_PAD_WIDTH, paddle1_pos - HALF_PAD_HEIGHT), (PAD_WIDTH - HALF_PAD_WIDTH, paddle1_pos + HALF_PAD_HEIGHT), PAD_WIDTH, "White")
    canvas.draw_line((WIDTH - PAD_WIDTH + HALF_PAD_WIDTH, paddle2_pos - HALF_PAD_HEIGHT), (WIDTH - PAD_WIDTH + HALF_PAD_WIDTH, paddle2_pos + HALF_PAD_HEIGHT), PAD_WIDTH, "White")
    #canvas.draw_line((PAD_WIDTH, paddle1_pos - HALF_PAD_HEIGHT), (PAD_WIDTH, paddle1_pos + HALF_PAD_HEIGHT), PAD_WIDTH, "White")
    #canvas.draw_line((WIDTH - PAD_WIDTH, paddle2_pos - HALF_PAD_HEIGHT), (WIDTH - PAD_WIDTH, paddle2_pos + HALF_PAD_HEIGHT), PAD_WIDTH, "White")
    
    # determine whether paddle and ball collide    
    if(ball_pos[0] - BALL_RADIUS <= PAD_WIDTH):
        if((ball_pos[1] >= paddle1_pos - HALF_PAD_HEIGHT) and (ball_pos[1] <= paddle1_pos + HALF_PAD_HEIGHT)):
            ball_vel[0] = ball_vel[0] * .5 + ball_vel[0]
    if(ball_pos[0] + BALL_RADIUS >= WIDTH - PAD_WIDTH):
        if((ball_pos[1] >= paddle2_pos - HALF_PAD_HEIGHT) and (ball_pos[1] <= paddle2_pos + HALF_PAD_HEIGHT)):
            ball_vel[0] * .5 + ball_vel[0]
    
    # draw scores
    canvas.draw_text(str(score2), [WIDTH / 3, HEIGHT / 3], 50, "White")
    canvas.draw_text(str(score1), [2*WIDTH / 3, HEIGHT / 3], 50, "White")
        
def keydown(key):
    global paddle1_vel, paddle2_vel
    
    if(key == simplegui.KEY_MAP["w"]):
        paddle1_vel -= 1
    elif(key == simplegui.KEY_MAP["s"]):
        paddle1_vel += 1
    if(key == simplegui.KEY_MAP["up"]):
        paddle2_vel -= 1
    elif(key == simplegui.KEY_MAP["down"]):
        paddle2_vel += 1
    
def keyup(key):
    global paddle1_vel, paddle2_vel
        
    if(key == simplegui.KEY_MAP["w"]):
        paddle1_vel = 0 
    if(key == simplegui.KEY_MAP["s"]):
        paddle1_vel = 0
    if(key == simplegui.KEY_MAP["up"]):
        paddle2_vel = 0
    elif(key == simplegui.KEY_MAP["down"]):
        paddle2_vel = 0


# create frame
frame = simplegui.create_frame("Pong", WIDTH, HEIGHT)
frame.set_draw_handler(draw)
frame.set_keydown_handler(keydown)
frame.set_keyup_handler(keyup)
timer = simplegui.create_timer(100, tick)
reset_button = frame.add_button("Reset", new_game, 50)

# start frame
new_game()
timer.start()
frame.start()

