import pygame
import random
import sys

# Initialize pygame
pygame.init()

# Screen settings
WIDTH, HEIGHT = 600, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("🐍 Snake Game")

# Colors
WHITE = (255, 255, 255)
GREEN = (0, 200, 0)
RED = (200, 0, 0)
BLACK = (0, 0, 0)

# Clock for controlling speed
clock = pygame.time.Clock()
SNAKE_BLOCK = 20
SNAKE_SPEED = 12

# Font for score and messages
font = pygame.font.SysFont(None, 35)

def draw_message(msg, color, x, y):
    text = font.render(msg, True, color)
    screen.blit(text, [x, y])

def game_loop():
    # Starting position
    x = WIDTH // 2
    y = HEIGHT // 2
    dx = dy = 0

    snake = [(x, y)]
    snake_length = 1

    # Food position
    food_x = random.randrange(0, WIDTH - SNAKE_BLOCK, SNAKE_BLOCK)
    food_y = random.randrange(0, HEIGHT - SNAKE_BLOCK, SNAKE_BLOCK)

    running = True
    game_over = False

    while running:
        while game_over:
            screen.fill(BLACK)
            draw_message("Game Over! Press C to Play Again or Q to Quit", RED, 40, 150)
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
                        game_loop()  # Restart game safely

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT and dx == 0:
                    dx, dy = -SNAKE_BLOCK, 0
                elif event.key == pygame.K_RIGHT and dx == 0:
                    dx, dy = SNAKE_BLOCK, 0
                elif event.key == pygame.K_UP and dy == 0:
                    dx, dy = 0, -SNAKE_BLOCK
                elif event.key == pygame.K_DOWN and dy == 0:
                    dx, dy = 0, SNAKE_BLOCK

        # Move the snake
        x += dx
        y += dy

        # Check wall collision
        if x < 0 or x >= WIDTH or y < 0 or y >= HEIGHT:
            game_over = True

        # Draw everything
        screen.fill(WHITE)
        pygame.draw.rect(screen, RED, (food_x, food_y, SNAKE_BLOCK, SNAKE_BLOCK))

        # Update snake
        snake.append((x, y))
        if len(snake) > snake_length:
            del snake[0]

        # Check self collision
        if len(snake) != len(set(snake)):
            game_over = True

        # Draw snake
        for segment in snake:
            pygame.draw.rect(screen, GREEN, (segment[0], segment[1], SNAKE_BLOCK, SNAKE_BLOCK))

        # Eating food
        if x == food_x and y == food_y:
            food_x = random.randrange(0, WIDTH - SNAKE_BLOCK, SNAKE_BLOCK)
            food_y = random.randrange(0, HEIGHT - SNAKE_BLOCK, SNAKE_BLOCK)
            snake_length += 1

        # Draw score
        draw_message(f"Score: {snake_length - 1}", BLACK, 10, 10)

        pygame.display.update()
        clock.tick(SNAKE_SPEED)

    pygame.quit()
    sys.exit()

if __name__ == "__main__":
    game_loop()
