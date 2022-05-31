---
layout : post
title : '스파르타 첫 개인 프로젝트 pygame이용 doodle jump'
---

### 캐릭터, 바닥, 발판 생성.
### 캐릭터 움직임. 바닥과 발판을 아래로 이동
### 발판의 넓이와 위치를 랜덤하게 생성
### 화면 아래로 내려간 발판을 없애고 새로운 발판을 위쪽에 생성

#### 캐릭터가 발판을 밟는지를 판정하는데 시간 딜레이인지 시스템의 속도 문제인지 정확한 위치를 측정할 수 없어서 충분한 범위를 줌
#### 높은 곳에서 가속하며 내려올때는 판정가능한 순간이 짧아져서 발판을 뚫는 버그 있음
#### 발판의 생성 위치가 겹치는 등의 문제
#### 게임 시작 전 인트로 화면, 점수나 시간등을 통한 게임 기록 관리 추가를 해야 한다



```python
import pygame
import os
import random
import time

pygame.init()

screen_width = 320
screen_height = 480
screen = pygame.display.set_mode((screen_width, screen_height))

pygame.display.set_caption("Jump Jump")

clock = pygame.time.Clock()

current_path = os.path.dirname(__file__)  # 현재 파일의 위치 폴더 반환
image_path = os.path.join(current_path, "images")  # images 폴더 위치 반환

# 배경 만들기

background = pygame.image.load(os.path.join(image_path, "background_resize.jpg"))

# ground, land 아래로
gravity = 0.0
land_down_speed = 0

# ground 만들기
ground = pygame.image.load(os.path.join(image_path, "black_ground.jpg"))
ground_size = ground.get_rect().size
ground_height = ground_size[1]
ground_y_pos = screen_height - ground_height

# 캐릭터 만들기

character = pygame.image.load(os.path.join(image_path, "red_character.jpg"))
character_size = character.get_rect().size
character_width = character_size[0]
character_height = character_size[1]
character_x_pos = (screen_width - character_width) / 2
character_y_pos = screen_height - character_height - ground_height

character_to_x = 0

character_speed = 0.5

# 캐릭터 점프

character_to_y = 0
characterOnTheLand = False


# 발판 생성
class LandToStep:
    global land_down_speed

    def __init__(self, x_pos, y_pos, land_width, land_height):
        self.land_image = pygame.Surface((land_width, land_height))
        self.land_width = land_width
        self.land_height = land_height
        self.x_pos = x_pos
        self.y_pos = y_pos

    def image(self):
        return self.land_image, self.land_width, self.land_height

    def pos(self):
        return [self.x_pos, self.y_pos + land_down_speed]


lands = []
# land_x_position = [140, 140, 140, 140]
for i in range(4):
    land_x_position = random.randint(10, 300)
    var_height = random.randint(-40, 40)
    land_width = random.randint(40, 100)

    land = LandToStep(land_x_position, 480 - 100 * (i + 1) + var_height, land_width, 20)
    lands.append([land.image(), land.pos()])



# 폰트 정의
game_font = pygame.font.Font(None, 40)  # None 쓰면 기본 폰트. 40은 크기

# 총 시간
total_time = 100

# 시작 시간 정보
start_ticks = pygame.time.get_ticks()  # 시작 tick을 받아옴

# 이벤트 루프
running = True
while running:
    dt = clock.tick(60)  # 초당 프레임
    # print("fps : "+ str(clock.get_fps()))
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                character_to_x -= character_speed
            elif event.key == pygame.K_RIGHT:
                character_to_x += character_speed

        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                character_to_x = 0
            elif event.key == pygame.K_UP or event.key == pygame.K_DOWN:
                character_to_y = 0
            elif event.key == pygame.K_SPACE:
                gravity = 0.5
                if 0 <= character_to_y <= 0.55 :
                    character_to_y = -16

    # 캐릭터 좌우 이동
    character_x_pos += character_to_x * dt


    # 캐릭터 상하 이동
    if character_to_y != gravity:
        character_to_y += 0.8
    character_y_pos += character_to_y

    # 캐릭터가 화면 옆으로 나가지 않도록
    if character_x_pos < 0:
        character_x_pos = 0
    elif character_x_pos > screen_width - character_width:
        character_x_pos = screen_width - character_width

    # 캐릭터가 바닥 아래로 떨어지면 게임오버
    if character_y_pos > screen_height:
        game_over = game_font.render(str('GAME OVER'), True, (255, 255, 255))
        text_rect = game_over.get_rect(center=(screen_width / 2, screen_height / 2))
        screen.blit(game_over, text_rect)
        running = False
        pygame.display.update()  # 게임 화면을 다시 그리기
        pygame.time.delay(2000)  # 종료 전 2초 대기
        # font = pygame.font.Font(None, 25)
        # text = font.render("You win!", True, BLACK)
        # screen.blit(text, text_rect)

    # 새로운 발판 추가
    if len(lands) < 6:
        width = random.randint(40, 100)
        land_spawn = LandToStep(random.randrange(0, screen_width - width),
                                random.randrange(-50, -20),
                                width, 20)

        lands.append([land_spawn.image(), land_spawn.pos()])

    # 충돌 처리

    character_rect = character.get_rect()
    character_rect.left = character_x_pos
    character_rect.top = character_y_pos

    for land in lands:
        land_pos_x = land[1][0]
        # 바닥을 아래로 내리기
        land[1][1] += gravity
        land_pos_y = land[1][1]

        # 화면 아래로 내려간 발판 삭제
        if land_pos_y > screen_height:
            lands.pop(0)


        # 발판 rect 정보 업데이트
        land_rect = land[0][0].get_rect()
        land_rect.left = land_pos_x
        land_rect.top = land_pos_y

    # 캐릭터가 발판에 올라서는가 판정
        if character_to_y >= -1.8:
            if land_pos_y + 9 >= character_y_pos + character_height >= land_pos_y - 5:
                if land[0][1] + land_pos_x + character_width >= character_x_pos + character_width >= land_pos_x:
                    character_to_y = gravity
                    character_to_y += 0
                    characterOnTheLand = True
                    landCharacterOn_pos = land_pos_x
                    land_size_x = land[0][1]

    # 캐릭터가 발판을 벗어나면 떨어지도록
    if characterOnTheLand:
        if land_size_x + landCharacterOn_pos + character_width <= character_x_pos + character_width or character_x_pos + character_width <= landCharacterOn_pos:
            character_to_y += 0.8
            characterOnTheLand = False

    ground_y_pos += gravity

    screen.blit(background, (0, 0))  # 배경그리기
    screen.blit(ground, (0, ground_y_pos))  # 바닥그리기
    screen.blit(character, (character_x_pos, character_y_pos))  # 캐릭터 그리기
    for land in lands:
        screen.blit(land[0][0], (land[1][0], land[1][1] ))  # 발판 그리기


    pygame.display.update()  # 게임 화면을 다시 그리기

pygame.time.delay(2000)  # 종료 전 2초 대기

pygame.quit()
```
