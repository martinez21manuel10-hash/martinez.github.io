import pygame
import time
import random

# 1. Configuración de Pygame y la ventana
pygame.init()

# Definición de colores (RGB)
BLANCO = (255, 255, 255)
AMARILLO = (255, 255, 102)
NEGRO = (0, 0, 0)
ROJO = (213, 50, 80)
VERDE = (0, 255, 0)
AZUL = (50, 153, 213)

# Dimensiones de la ventana
ancho_ventana = 600
alto_ventana = 400
ventana = pygame.display.set_mode((ancho_ventana, alto_ventana))
pygame.display.set_caption('Juego de la Serpiente')

# 2. Configuración de la Serpiente y el Juego
reloj = pygame.time.Clock()
tamano_serpiente = 10
velocidad_serpiente = 15 # Cuadros por segundo

# Fuente para el texto
fuente_estilo = pygame.font.SysFont("bahnschrift", 25)
fuente_puntuacion = pygame.font.SysFont("comicsansms", 35)

def mostrar_puntuacion(puntuacion):
    """Muestra la puntuación actual en la esquina superior izquierda."""
    valor = fuente_puntuacion.render("Puntuación: " + str(puntuacion), True, AMARILLO)
    ventana.blit(valor, [0, 0])

def dibujar_serpiente(tamano_serpiente, lista_serpiente):
    """Dibuja cada segmento de la serpiente."""
    for x in lista_serpiente:
        pygame.draw.rect(ventana, VERDE, [x[0], x[1], tamano_serpiente, tamano_serpiente])

def mensaje(msg, color):
    """Muestra un mensaje en el centro de la pantalla."""
    mesg = fuente_estilo.render(msg, True, color)
    rect_msg = mesg.get_rect(center=(ancho_ventana / 2, alto_ventana / 2))
    ventana.blit(mesg, rect_msg)

# 3. Bucle Principal del Juego
def bucle_juego():
    """Contiene toda la lógica del juego."""
    juego_terminado = False
    juego_cerrado = False

    # Posición inicial de la serpiente
    x1 = ancho_ventana / 2
    y1 = alto_ventana / 2

    # Cambio de posición (movimiento)
    x1_cambio = 0
    y1_cambio = 0

    # Estructura de la serpiente
    lista_serpiente = []
    longitud_serpiente = 1

    # Posición de la comida (manzana)
    comida_x = round(random.randrange(0, ancho_ventana - tamano_serpiente) / 10.0) * 10.0
    comida_y = round(random.randrange(0, alto_ventana - tamano_serpiente) / 10.0) * 10.0

    while not juego_terminado:

        # Bucle de pantalla de "Game Over"
        while juego_cerrado == True:
            ventana.fill(AZUL)
            mensaje("¡Perdiste! Presiona C para Jugar de Nuevo o Q para Salir", ROJO)
            mostrar_puntuacion(longitud_serpiente - 1)
            pygame.display.update()

            for evento in pygame.event.get():
                if evento.type == pygame.QUIT:
                    juego_terminado = True
                    juego_cerrado = False
                if evento.type == pygame.KEYDOWN:
                    if evento.key == pygame.K_q:
                        juego_terminado = True
                        juego_cerrado = False
                    if evento.key == pygame.K_c:
                        bucle_juego() # Reinicia el juego

        # Bucle de Eventos (Entrada del Usuario)
        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                juego_terminado = True
            if evento.type == pygame.KEYDOWN:
                if evento.key == pygame.K_LEFT:
                    x1_cambio = -tamano_serpiente
                    y1_cambio = 0
                elif evento.key == pygame.K_RIGHT:
                    x1_cambio = tamano_serpiente
                    y1_cambio = 0
                elif evento.key == pygame.K_UP:
                    y1_cambio = -tamano_serpiente
                    x1_cambio = 0
                elif evento.key == pygame.K_DOWN:
                    y1_cambio = tamano_serpiente
                    x1_cambio = 0

        # 4. Lógica del Juego

        # Comprueba si golpea los bordes
        if x1 >= ancho_ventana or x1 < 0 or y1 >= alto_ventana or y1 < 0:
            juego_cerrado = True

        # Actualiza la posición de la serpiente
        x1 += x1_cambio
        y1 += y1_cambio
        ventana.fill(NEGRO) # Fondo de la ventana

        # Dibuja la comida
        pygame.draw.rect(ventana, ROJO, [comida_x, comida_y, tamano_serpiente, tamano_serpiente])

        # Actualiza la lista de la serpiente
        cabeza_serpiente = []
        cabeza_serpiente.append(x1)
        cabeza_serpiente.append(y1)
        lista_serpiente.append(cabeza_serpiente)

        # Mantiene la longitud correcta
        if len(lista_serpiente) > longitud_serpiente:
            del lista_serpiente[0]

        # Comprueba si la serpiente se muerde a sí misma
        for x in lista_serpiente[:-1]:
            if x == cabeza_serpiente:
                juego_cerrado = True

        # Dibuja la serpiente
        dibujar_serpiente(tamano_serpiente, lista_serpiente)
        mostrar_puntuacion(longitud_serpiente - 1)

        pygame.display.update()

        # Comprueba si come la comida
        if x1 == comida_x and y1 == comida_y:
            comida_x = round(random.randrange(0, ancho_ventana - tamano_serpiente) / 10.0) * 10.0
            comida_y = round(random.randrange(0, alto_ventana - tamano_serpiente) / 10.0) * 10.0
            longitud_serpiente += 1 # Hace crecer a la serpiente

        reloj.tick(velocidad_serpiente) # Controla la velocidad

    # 5. Salida del Juego
    pygame.quit()
    quit()

bucle_juego()
