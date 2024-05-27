import pygame
import random
import sys

# Inicializar pygame
pygame.init()

# Definir colores
NEGRO = (0, 0, 0)
BLANCO = (255, 255, 255)

# Definir dimensiones de la pantalla
ANCHO = 800
ALTO = 600

# Definir la velocidad del jugador y los barriles
VELOCIDAD_JUGADOR = 5
VELOCIDAD_BARRIL = 7

# Clase para el jugador
class Jugador(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(BLANCO)
        self.rect = self.image.get_rect()
        self.rect.centerx = ANCHO // 2
        self.rect.bottom = ALTO - 10

    def update(self):
        # Mover el jugador a la izquierda o derecha
        teclas = pygame.key.get_pressed()
        if teclas[pygame.K_LEFT] and self.rect.left > 0:
            self.rect.x -= VELOCIDAD_JUGADOR
        if teclas[pygame.K_RIGHT] and self.rect.right < ANCHO:
            self.rect.x += VELOCIDAD_JUGADOR

# Clase para los barriles
class Barril(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((30, 30))
        self.image.fill(NEGRO)
        self.rect = self.image.get_rect()
        self.rect.x = random.randrange(0, ANCHO - self.rect.width)
        self.rect.y = random.randrange(-100, -40)
        self.velocidad_y = random.randrange(VELOCIDAD_BARRIL // 2, VELOCIDAD_BARRIL)

    def update(self):
        # Mover el barril hacia abajo
        self.rect.y += self.velocidad_y
        # Si el barril se sale de la pantalla, reiniciarlo en la parte superior
        if self.rect.top > ALTO + 10:
            self.rect.x = random.randrange(0, ANCHO - self.rect.width)
            self.rect.y = random.randrange(-100, -40)
            self.velocidad_y = random.randrange(VELOCIDAD_BARRIL // 2, VELOCIDAD_BARRIL)

# Inicializar pantalla
pantalla = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Salto en el Desierto")

# Crear objetos
jugador = Jugador()
barriles = pygame.sprite.Group()
todos_los_sprites = pygame.sprite.Group()
todos_los_sprites.add(jugador)

# Loop principal del juego
reloj = pygame.time.Clock()
puntuacion = 0

while True:
    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    # Generar nuevos barriles
    if random.randint(0, 100) < 10:
        barril = Barril()
        barriles.add(barril)
        todos_los_sprites.add(barril)

    # Actualizar sprites
    todos_los_sprites.update()

    # Colisiones
    colisiones = pygame.sprite.spritecollide(jugador, barriles, True)
    for barril in colisiones:
        puntuacion += 1

    # Limpiar pantalla
    pantalla.fill(BLANCO)

    # Dibujar sprites en pantalla
    todos_los_sprites.draw(pantalla)

    # Mostrar puntuación
    fuente = pygame.font.Font(None, 36)
    texto = fuente.render("Puntuación: " + str(puntuacion), True, NEGRO)
    pantalla.blit(texto, (10, 10))

    # Actualizar pantalla
    pygame.display.flip()

    # Controlar la velocidad de fotogramas
    reloj.tick(30)

