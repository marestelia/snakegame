# snakegame
import pygame
import random

ekran_genislik = 640
ekran_yukseklik = 480

siyah = (0, 0, 0)
yesil = (0, 255, 0)
kirmizi = (255, 0, 0)

baslangic_x = ekran_genislik / 2
baslangic_y = ekran_yukseklik / 2
yilan_boyut = 20

hiz = 20

pygame.init()

ekran = pygame.display.set_mode((ekran_genislik, ekran_yukseklik))
pygame.display.set_caption("Yılan Oyunu")

saat = pygame.time.Clock()

def renk_sec():
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                return None

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_k:
                    return (255, 0, 0)  # Kırmızı
                elif event.key == pygame.K_m:
                    return (0, 0, 255)  # Mavi
                elif event.key == pygame.K_c:
                    return (128, 0, 128)  # Mor
                elif event.key == pygame.K_s:
                    return (255, 255, 0)  # Sarı
                elif event.key == pygame.K_p:
                    return (255, 20, 147)  # Pembe
                elif event.key == pygame.K_y:
                    return (0, 255, 0)  # Yeşil

        ekran.fill(siyah)

        font = pygame.font.Font(None, 25)
        secim_metni = font.render("Renk seçin: K-Kırmızı, M-Mavi, C-Mor,S-Sarı, P-Pembe, Y-Yeşil", True, yesil)
        ekran.blit(secim_metni, (10, 10))

        pygame.display.update()

        saat.tick(10)

def oyuna_basla():
    yilan_x = [baslangic_x]
    yilan_y = [baslangic_y]
    yon_x = 0
    yon_y = 0

    yem_x = round(random.randrange(0, ekran_genislik - yilan_boyut) / 20) * 20
    yem_y = round(random.randrange(0, ekran_yukseklik - yilan_boyut) / 20) * 20

    skor = 0

    secilen_renk = renk_sec()
    if secilen_renk is None:
        return False

    oyun_devam = True
    while oyun_devam:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                return False

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT and yon_x != hiz:
                    yon_x = -hiz
                    yon_y = 0
                elif event.key == pygame.K_RIGHT and yon_x != -hiz:
                    yon_x = hiz
                    yon_y = 0
                elif event.key == pygame.K_UP and yon_y != hiz:
                    yon_x = 0
                    yon_y = -hiz
                elif event.key == pygame.K_DOWN and yon_y != -hiz:
                    yon_x = 0
                    yon_y = hiz

        yeni_x = yilan_x[0] + yon_x
        yeni_y = yilan_y[0] + yon_y

        if yeni_x < 0 or yeni_x >= ekran_genislik or yeni_y < 0 or yeni_y >= ekran_yukseklik:
            oyun_devam = False

        if (yeni_x, yeni_y) in zip(yilan_x, yilan_y[1:]):
            oyun_devam = False

        yilan_x.insert(0, yeni_x)
        yilan_y.insert(0, yeni_y)

        if yeni_x == yem_x and yeni_y == yem_y:
            skor += 1
            yem_x = round(random.randrange(0, ekran_genislik - yilan_boyut) / 20) * 20
            yem_y = round(random.randrange(0, ekran_yukseklik - yilan_boyut) / 20) * 20
        else:
            yilan_x.pop()
            yilan_y.pop()

        ekran.fill(siyah)

        for i in range(len(yilan_x)):
            pygame.draw.rect(ekran, secilen_renk, (yilan_x[i], yilan_y[i], yilan_boyut, yilan_boyut))

        pygame.draw.rect(ekran, kirmizi, (yem_x, yem_y, yilan_boyut, yilan_boyut))

        font = pygame.font.Font(None, 36)
        skor_metni = font.render("Skor: " + str(skor), True, yesil)
        ekran.blit(skor_metni, (10, 10))

        pygame.display.update()

        saat.tick(10)

    font = pygame.font.Font(None, 48)
    tekrar_oyna_metni = font.render("Tekrar Oyna", True, yesil)
    tekrar_oyna_rect = tekrar_oyna_metni.get_rect(center=(ekran_genislik/2, ekran_yukseklik/2))
    ekran.blit(tekrar_oyna_metni, tekrar_oyna_rect)
    pygame.display.update()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                return False

            if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
                if tekrar_oyna_rect.collidepoint(event.pos):
                    return True

oyun_bittimi = False
while not oyun_bittimi:
    oyun_bittimi = not oyuna_basla()

pygame.quit()
