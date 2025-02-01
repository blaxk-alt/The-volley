# The-volley
Best volleyball game
pip install pygame
import pygame
import random

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Simple Volleyball Game")

# Colors
WHITE = (255, 255, 255)
BLUE = (50, 100, 255)
RED = (255, 50, 50)
YELLOW = (255, 255, 0)

# Game variables
gravity = 0.5
ball_speed_x = 4 * random.choice((1, -1))
ball_speed_y = -5
player_speed = 5
jump_force = -10

# Player properties
player1 = pygame.Rect(100, HEIGHT - 80, 40, 80)
player2 = pygame.Rect(600, HEIGHT - 80, 40, 80)
ball = pygame.Rect(WIDTH//2, HEIGHT//2, 20, 20)

# Net
net = pygame.Rect(WIDTH//2 - 5, HEIGHT - 120, 10, 120)

# Velocities
p1_velocity_y = 0
p2_velocity_y = 0

# Ball velocity
ball_dx, ball_dy = ball_speed_x, ball_speed_y

# Clock for controlling FPS
clock = pygame.time.Clock()

running = True
while running:
    clock.tick(60)  # 60 FPS
    screen.fill(WHITE)

    # Event Handling
    keys = pygame.key.get_pressed()
    
    # Player 1 Controls (WASD)
    if keys[pygame.K_a] and player1.left > 0:
        player1.x -= player_speed
    if keys[pygame.K_d] and player1.right < WIDTH//2 - 10:
        player1.x += player_speed
    if keys[pygame.K_w] and player1.bottom >= HEIGHT:
        p1_velocity_y = jump_force  # Jump

    # Player 2 Controls (Arrow Keys)
    if keys[pygame.K_LEFT] and player2.left > WIDTH//2 + 10:
        player2.x -= player_speed
    if keys[pygame.K_RIGHT] and player2.right < WIDTH:
        player2.x += player_speed
    if keys[pygame.K_UP] and player2.bottom >= HEIGHT:
        p2_velocity_y = jump_force  # Jump

    # Apply Gravity
    p1_velocity_y += gravity
    p2_velocity_y += gravity
    player1.y += p1_velocity_y
    player2.y += p2_velocity_y

    # Stop players from falling below ground
    if player1.bottom >= HEIGHT:
        player1.bottom = HEIGHT
        p1_velocity_y = 0
    if player2.bottom >= HEIGHT:
        player2.bottom = HEIGHT
        p2_velocity_y = 0

    # Ball movement
    ball.x += ball_dx
    ball.y += ball_dy
    ball_dy += gravity  # Apply gravity to ball

    # Ball collisions with walls
    if ball.left <= 0 or ball.right >= WIDTH:
        ball_dx *= -1  # Bounce off walls
    if ball.top <= 0:
        ball_dy *= -1  # Bounce off ceiling

    # Ball collision with ground (reset position)
    if ball.bottom >= HEIGHT:
        ball.x, ball.y = WIDTH//2, HEIGHT//2
        ball_dx, ball_dy = ball_speed_x, ball_speed_y  # Reset speed

    # Ball collision with net
    if ball.colliderect(net):
        ball_dx *= -1

    # Ball collision with players
    if ball.colliderect(player1) or ball.colliderect(player2):
        ball_dx *= -1
        ball_dy = jump_force * 0.8  # Bounce up

    # Draw elements
    pygame.draw.rect(screen, BLUE, player1)
    pygame.draw.rect(screen, RED, player2)
    pygame.draw.rect(screen, YELLOW, ball)
    pygame.draw.rect(screen, (0, 0, 0), net)

    # Update display
    pygame.display.flip()

    # Check for quitting
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

pygame.quit()
