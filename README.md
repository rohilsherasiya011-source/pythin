import pygame
import random
import sys

# Initialize pygame
pygame.init()

# Screen setup
WIDTH, HEIGHT = 600, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("🐍 Snake Game")

# Colors
WHITE = (255, 255, 255)
GREEN = (0, 200, 0)
RED = (200, 0, 0)
BLACK = (0, 0, 0)

# Clock for speed control
clock = pygame.time.Clock()
snake_block = 20
snake_speed = 12

font = pygame.font.SysFont(None, 35)

def message(msg, color, x, y):
    text = font.render(msg, True, color)
    screen.blit(text, [x, y])

def game_loop():
    x = WIDTH // 2
    y = HEIGHT // 2
    dx, dy = 0, 0

    snake = [(x, y)]
    length = 1

    food_x = random.randrange(0, WIDTH - snake_block, snake_block)
    food_y = random.randrange(0, HEIGHT - snake_block, snake_block)

    running = True
    game_over = False

    while running:
        while game_over:
            screen.fill(BLACK)
            message("Game Over! Press C to Play Again or Q to Quit", RED, 60, 150)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    pygame.quit()
                    sys.exit()
                elif event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        pygame.quit()
                        sys.exit()
                    elif event.key == pygame.K_c:
                        game_loop()  # restart game

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT and dx == 0:
                    dx, dy = -snake_block, 0
                elif event.key == pygame.K_RIGHT and dx == 0:
                    dx, dy = snake_block, 0
                elif event.key == pygame.K_UP and dy == 0:
                    dx, dy = 0, -snake_block
                elif event.key == pygame.K_DOWN and dy == 0:
                    dx, dy = 0, snake_block

        # move
        x += dx
        y += dy

        # check wall collision
        if x < 0 or x >= WIDTH or y < 0 or y >= HEIGHT:
            game_over = True

        # draw everything
        screen.fill(WHITE)
        pygame.draw.rect(screen, RED, (food_x, food_y, snake_block, snake_block))

        # snake update
        snake.append((x, y))
        if len(snake) > length:
            del snake[0]

        # check self collision
        if len(snake) != len(set(snake)):
            game_over = True

        # draw snake
        for s in snake:
            pygame.draw.rect(screen, GREEN, (s[0], s[1], snake_block, snake_block))

        # eat food
        if x == food_x and y == food_y:
            food_x = random.randrange(0, WIDTH - snake_block, snake_block)
            food_y = random.randrange(0, HEIGHT - snake_block, snake_block)
            length += 1

        message(f"Score: {length - 1}", BLACK, 10, 10)
        pygame.display.update()
        clock.tick(snake_speed)

    pygame.quit()
    sys.exit()

game_loop()
