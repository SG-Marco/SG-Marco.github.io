---
layout : single
title : 'Doodle Jump 마무리'
---

#### 구현한 기능
+ 시작 화면에서 난이도 선택 가능
+ mongoDB를 이용해 highscore 기록 및 표현
+ fake_screen을 이용해서 전체화면이나 화면크기 전환시에도 내용물이 scale 되도록
+ 난이도에 따른 수치 조절
+ 높이 올라갈수록 난이도 점점 상승
+ 점수에 따라 배경화면 변경
+ Pause, Start 기능
+ 배경음악과 효과음 추가




최고득점을 표현하면서 여러줄을 한번에 넣는 방법을 찾아 보았다.
시간날 때 음미해 볼것
```python
# 텍스트 여러줄을 한번에 쓰기
def blit_text(surface, text, pos, font, color=pygame.Color(WHITE)):
    words = [word.split(' ') for word in text.splitlines()]  # 2D array where each row is a list of words.
    space = font.size(' ')[0]  # The width of a space.
    max_width, max_height = surface.get_size()
    x, y = pos
    for line in words:
        for word in line:
            word_surface = font.render(word, 0, color)
            word_width, word_height = word_surface.get_size()
            if x + word_width >= max_width:
                x = pos[0]  # Reset the x.
                y += word_height  # Start on new row.
            surface.blit(word_surface, (x, y))
            x += word_width + space
        x = pos[0]  # Reset the x.
        y += word_height  # Start on new row.
```


화면에 blit을 하는 순서가 중요하다. 나중에 한 것이 위에 올라간다.

blit은 screen에만 할 필요가 없다.fake_screen을 만들어도 되고, image에 해도 된다.

최고점수의 db를 업데이트 할때
```python
        db.doodles.update_one({'easy' : easy_score}, {'$set': {'easy': howManySteps}})
```
이런 방식으로 원하는 값을 직접 찾아도 된다.


---
#### 이하 전체 코드. 코드가 너무 지저분해서 스스로도 찾기 힘들다. 깔끔한 정리 방식이 필요하다.
```python
import pygame
import os
import random
from pymongo import MongoClient

client = MongoClient('mongodb+srv://test:sparta@cluster0.nqwfa.mongodb.net/Cluster0?retryWrites=true&w=majority')
db = client.dbsparta

high_scores = db.doodles.find({}, {'_id': False})
easy_score = high_scores[0]['easy']
hard_score = high_scores[0]['hard']

pygame.init()

# 사운드
pygame.mixer.init()
pygame.mixer.music.load('sounds/bgm.mp3')
pygame.mixer.music.set_volume(0.2)
pygame.mixer.music.play(-1)
jump_sound = pygame.mixer.Sound('sounds/jump.wav')
gameover_sound = pygame.mixer.Sound('sounds/gameover.wav')

BG = (50, 50, 50)
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)

screen_width = 800
screen_height = 800
screen = pygame.display.set_mode((screen_width, screen_height), pygame.RESIZABLE, pygame.FULLSCREEN)  # 리사이즈, 전체화면 기능
# 화면 사이즈에 맞게 컨텐츠 확장하도록. 안그러면 픽셀이 그대로 유지됨
fake_screen = screen.copy()

pygame.display.set_caption("Jump Jump")

clock = pygame.time.Clock()

current_path = os.path.dirname(__file__)  # 현재 파일의 위치 폴더 반환
image_path = os.path.join(current_path, "images")  # images 폴더 위치 반환


# 게임 오버 함수
def game_over():
    global running, howManySteps, easy_score, hard_score, difficulty

    gameover_sound.play()

    game_over = gameover_font.render(str('GAME OVER'), True, (255, 255, 255))
    game_over_text_rect = game_over.get_rect(center=(screen_width / 2, screen_height / 2))

    end_step = end_step_font.render(str(f'Your level is {howManySteps}!'), True, (213, 213, 213))
    end_step_text_rect = end_step.get_rect(center=(screen_width / 2, (screen_height / 2) + 50))

    highscore_text = highscore_end_font.render(str('HIGHSCORE!!!'), True, (255, 0, 0))
    highscore_text_rect = highscore_text.get_rect(center=(screen_width / 2, (screen_height / 2) + 100))
    if difficulty == 'easy' and howManySteps > easy_score:
        fake_screen.blit(highscore_text, highscore_text_rect)
        db.doodles.update_one({'easy' : easy_score}, {'$set': {'easy': howManySteps}})

    elif difficulty == 'hard' and howManySteps > hard_score:
        fake_screen.blit(highscore_text, highscore_text_rect)
        db.doodles.update_one({'hard' : hard_score}, {'$set': {'hard': howManySteps}})

    fake_screen.blit(game_over, game_over_text_rect)
    fake_screen.blit(end_step, end_step_text_rect)
    screen.blit(pygame.transform.scale(fake_screen, screen.get_rect().size), (0, 0))
    pygame.display.flip()

    running = False

    pygame.display.update()  # 게임 화면을 다시 그리기
    pygame.time.delay(2000)  # 종료 전 2초 대기


# 배경 만들기
background1 = pygame.image.load(os.path.join(image_path, "bg1.jpg"))
background2 = pygame.image.load(os.path.join(image_path, "bg2.jpg"))
background3 = pygame.image.load(os.path.join(image_path, "bg3.jpg"))
background4 = pygame.image.load(os.path.join(image_path, "bg4.jpg"))

# ground, land 아래로
gravity = 0.0
land_down_speed = 0

# ground 만들기
ground = pygame.image.load(os.path.join(image_path, "ground.jpg"))
ground_size = ground.get_rect().size
ground_height = ground_size[1]
ground_y_pos = screen_height - ground_height

# 캐릭터 만들기

character_image = pygame.image.load('images/squid-game.png').convert_alpha()  # 시작화면 캐릭터 - 오징어게임

character = pygame.image.load(os.path.join(image_path, "squid-game-resize.png"))
character_size = character.get_rect().size
character_width = character_size[0]
character_height = character_size[1]
character_x_pos = (screen_width - character_width) / 2
character_y_pos = screen_height - character_height - ground_height

character_to_x = 0

character_speed = 5.5
howManySteps = 0

# 캐릭터 점프
character_to_y = 0
characterOnTheLand = False  # 캐릭터가 발판 위에 있는지 판정


def get_image(sheet, width, height):
    image = pygame.Surface((width, height)).convert_alpha()
    image.blit(sheet, (0, 0), (0, 0, width, height))
    return image


land_image_source = pygame.image.load(os.path.join(image_path, "land.jpg"))


# 발판 생성
class LandToStep:
    global land_down_speed

    def __init__(self, x_pos, y_pos, land_width, land_height):
        self.land_image = get_image(land_image_source, land_width, land_height)
        self.land_width = land_width
        self.land_height = land_height
        self.x_pos = x_pos
        self.y_pos = y_pos

    def image(self):
        return self.land_image, self.land_width, self.land_height

    def pos(self):
        return [self.x_pos, self.y_pos + land_down_speed]


# 게임 시작시 나오는 발판들
lands = []
for i in range(7):
    land_x_position = random.randint(50, 700)
    var_height = random.randint(-30, 30)
    land_width = random.randint(110, 250)

    land = LandToStep(land_x_position, 750 - 80 * (i + 1) + var_height, land_width, 25)
    lands.append([land.image(), land.pos()])

# 폰트 정의
game_font = pygame.font.Font(None, 50)  # None 쓰면 기본 폰트. 40은 크기
gameover_font = pygame.font.Font(None, 70)
end_step_font = pygame.font.Font(None, 45)
font_difficulty = pygame.font.Font(None, 60)
highscore_end_font = pygame.font.Font(None, 50)
highscore_start_font = pygame.font.Font(None, 30)
pause_text = pygame.font.Font(None, 70).render('Pause', True, WHITE)
pause_text_rect = pause_text.get_rect(center=(screen_width / 2, screen_height / 2))

# 난이도 버튼
easy_button = pygame.Rect(480, 150, 200, 100)
hard_button = pygame.Rect(480, 400, 200, 100)

# 난이도에 따른 수치 조절. [easy, hard]
difficulty_nums = [
    {'character_to_y +': 0.68,
     'gravity': 0.71 + howManySteps * 0.05,
     'character_to_y': -22,
     'num_of_land': random.randint(8, 10)},
    {'character_to_y +': 0.8,
     'gravity': 0.83 + howManySteps * 0.08,
     'character_to_y': -19,
     'num_of_land': random.randint(7, 9)}
]

# 텍스트 여러줄을 한번에 쓰기
def blit_text(surface, text, pos, font, color=pygame.Color(WHITE)):
    words = [word.split(' ') for word in text.splitlines()]  # 2D array where each row is a list of words.
    space = font.size(' ')[0]  # The width of a space.
    max_width, max_height = surface.get_size()
    x, y = pos
    for line in words:
        for word in line:
            word_surface = font.render(word, 0, color)
            word_width, word_height = word_surface.get_size()
            if x + word_width >= max_width:
                x = pos[0]  # Reset the x.
                y += word_height  # Start on new row.
            surface.blit(word_surface, (x, y))
            x += word_width + space
        x = pos[0]  # Reset the x.
        y += word_height  # Start on new row.


# 게임 시작 화면
def start_display():
    global easy_score, hard_score
    fake_screen.fill(BG)

    pygame.draw.rect(fake_screen, WHITE, easy_button, width=3, border_radius=5)
    easy_text = font_difficulty.render(str('EASY'), True, WHITE)
    easy_text_rect = easy_text.get_rect(center=(580, 200))
    fake_screen.blit(easy_text, easy_text_rect)

    pygame.draw.rect(fake_screen, WHITE, hard_button, width=3, border_radius=5)
    hard_text = font_difficulty.render(str('HARD'), True, WHITE)
    hard_text_rect = hard_text.get_rect(center=(580, 450))
    fake_screen.blit(hard_text, hard_text_rect)

    fake_screen.blit(character_image, (50, 80))

    highscore_text = f'HIGH SCORES\n' \
                     f'EASY : {easy_score}\n' \
                     f'HARD : {hard_score}'
    high_scores_start_display = highscore_start_font.render(highscore_text, True, WHITE)
    # fake_screen.blit(high_scores_start_display, (600, 600))

    blit_text(fake_screen, highscore_text, (500, 600), highscore_start_font, WHITE)

    screen.blit(pygame.transform.scale(fake_screen, screen.get_rect().size), (0, 0))
    pygame.display.flip()


def check_buttons(pos):
    global start, difficulty

    if easy_button.collidepoint(pos):
        start = True
        difficulty = 'easy'
    elif hard_button.collidepoint(pos):
        start = True
        difficulty = 'hard'


# 메인 게임 함수
def main():
    global running, characterOnTheLand, character_x_pos, character_y_pos, ground_y_pos, character_to_x, character_to_y, gravity, howManySteps, land_size_x, landCharacterOn_pos, start, difficulty, difficulty_nums

    # 캐릭터 좌우 이동
    character_x_pos += character_to_x

    # 캐릭터 상하 이동
    if character_to_y != gravity:
        if difficulty == 'easy':
            character_to_y += difficulty_nums[0]['character_to_y +']
        elif difficulty == 'hard':
            character_to_y += difficulty_nums[1]['character_to_y +']
    character_to_y = min(character_to_y, 9)
    character_y_pos += character_to_y

    # 캐릭터가 화면 옆으로 나가지 않도록
    if character_x_pos < 0:
        character_x_pos = 0
    elif character_x_pos > screen_width - character_width:
        character_x_pos = screen_width - character_width

    # 캐릭터가 바닥 아래로 떨어지면 게임오버
    if character_y_pos > screen_height:
        game_over()

    # 새로운 발판 추가
    if difficulty == 'easy':
        num_of_land = difficulty_nums[0]['num_of_land']
    elif difficulty == 'hard':
        num_of_land = difficulty_nums[1]['num_of_land']

    if len(lands) < num_of_land:
        width = random.randint(110, 250)
        land_spawn = LandToStep(random.randrange(0, screen_width - width),
                                random.randrange(-50, -28),
                                width, 25)
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
            howManySteps += 1

        # 발판 rect 정보 업데이트
        land_rect = land[0][0].get_rect()
        land_rect.left = land_pos_x
        land_rect.top = land_pos_y

        # 캐릭터가 발판에 올라서는가 판정
        if character_to_y >= -0.5:
            if land_pos_y + 7 >= character_y_pos + character_height >= land_pos_y - 2:
                if land[0][1] + land_pos_x + character_width >= character_x_pos + character_width >= land_pos_x:
                    if difficulty == 'easy':
                        character_to_y = difficulty_nums[0]['gravity']
                    elif difficulty == 'hard':
                        character_to_y = difficulty_nums[1]['gravity']

                    character_to_y += 0
                    characterOnTheLand = True
                    landCharacterOn_pos = land_pos_x
                    land_size_x = land[0][1]

    # 캐릭터가 발판을 벗어나면 떨어지도록
    if characterOnTheLand:
        if land_size_x + landCharacterOn_pos + character_width <= character_x_pos + character_width or character_x_pos + character_width <= landCharacterOn_pos:
            if difficulty == 'easy':
                character_to_y += difficulty_nums[0]['character_to_y +']
            elif difficulty == 'hard':
                character_to_y += difficulty_nums[1]['character_to_y +']

            characterOnTheLand = False

    ground_y_pos += gravity

    if howManySteps < 20:
        fake_screen.blit(background1, (0, 0))  # 배경그리기
    elif 20 <= howManySteps < 40:
        fake_screen.blit(background2, (0, 0))
    elif 40 <= howManySteps < 60:
        fake_screen.blit(background3, (0, 0))
    elif 60 <= howManySteps:
        fake_screen.blit(background4, (0, 0))

    fake_screen.blit(ground, (0, ground_y_pos))  # 바닥그리기
    fake_screen.blit(character, (character_x_pos, character_y_pos))  # 캐릭터 그리기
    for land in lands:
        fake_screen.blit(land[0][0], (land[1][0], land[1][1]))  # 발판 그리기

    score = game_font.render(str(int(howManySteps)), True, (255, 255, 255))
    fake_screen.blit(score, (20, 20))
    screen.blit(pygame.transform.scale(fake_screen, screen.get_rect().size), (0, 0))
    pygame.display.flip()


# 이벤트 루프
start = False
running = True
difficulty = None
characterOnTheGround = True
RUNNING, PAUSE = 0, 1
state = RUNNING
print('start')
while running:
    dt = clock.tick(60)  # 초당 프레임
    click_pos = None
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONUP:
            click_pos = pygame.mouse.get_pos()

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                character_to_x -= character_speed
            elif event.key == pygame.K_RIGHT:
                character_to_x += character_speed
            elif event.key == pygame.K_SPACE:
                if difficulty == 'easy':
                    gravity = difficulty_nums[0]['gravity']
                elif difficulty == 'hard':
                    gravity = difficulty_nums[1]['gravity']

                if characterOnTheGround:
                    jump_sound.play()
                    characterOnTheGround = False
                    character_to_y = -22
                elif characterOnTheLand:
                    characterOnTheLand = False
                    jump_sound.play()
                    if difficulty == 'easy':
                        character_to_y = difficulty_nums[0]['character_to_y']
                    elif difficulty == 'hard':
                        character_to_y = difficulty_nums[1]['character_to_y']
            # pause, start
            if start:
                if event.key == pygame.K_p:
                    state = PAUSE
                elif event.key == pygame.K_s:
                    state = RUNNING

        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                character_to_x = 0

    if click_pos:
        check_buttons(click_pos)

    if not start:
        start_display()
    elif start:
        if state == RUNNING:
            pygame.mixer.music.unpause()
            main()
        elif state == PAUSE:
            pygame.mixer.music.pause()
            screen.blit(pause_text, pause_text_rect)

    pygame.display.update()  # 게임 화면을 다시 그리기

pygame.time.delay(2000)  # 종료 전 2초 대기

pygame.quit()
```
