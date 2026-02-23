#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h> // bool veri tipi icin

// Ogrenci yapisi
typedef struct Ogrenci {
    int numara;
    char tcKimlik[12];
    char ad[50];
    char soyad[50];
    char dersler[100]; // Alinan dersler
    char bolum[50];
    struct Ogrenci* onceki; // Onceki node
    struct Ogrenci* sonraki; // Sonraki node
} Ogrenci;

Ogrenci* baslangic = NULL;

// Yeni bir ogrenci olustur ve dondur
Ogrenci* yeniOgrenciOlustur(int numara, char* tcKimlik, char* ad, char* soyad, char* dersler, char* bolum) {
    Ogrenci* yeniOgr = (Ogrenci*)malloc(sizeof(Ogrenci));
    if (yeniOgr == NULL) {
        perror("Bellek ayirma hatasi");
        exit(EXIT_FAILURE);
    }
    yeniOgr->numara = numara;
    strcpy(yeniOgr->tcKimlik, tcKimlik);
    strcpy(yeniOgr->ad, ad);
    strcpy(yeniOgr->soyad, soyad);
    strcpy(yeniOgr->dersler, dersler);
    strcpy(yeniOgr->bolum, bolum);
    yeniOgr->onceki = NULL;
    yeniOgr->sonraki = NULL;
    return yeniOgr;
}

// ogrenci ekleme fonksiyonu (sona ekleme)
void ogrenciEkle(int numara, char* tcKimlik, char* ad, char* soyad, char* dersler, char* bolum) {
    Ogrenci* yeniOgr = yeniOgrenciOlustur(numara, tcKimlik, ad, soyad, dersler, bolum);
    if (baslangic == NULL) {
        baslangic = yeniOgr;
    } else {
        Ogrenci* temp = baslangic;
        while (temp->sonraki != NULL) { // Sonraki node'u bulana kadar ilerle
            temp = temp->sonraki;
        }
        // Son node'u bulduktan sonra yeni ogrenciyi ekle
        temp->sonraki = yeniOgr;
        yeniOgr->onceki = temp;  // Yeni ogrencinin onceki node'unu ayarla
    }
}

// ogrencileri listeleme fonksiyonu
void ogrencileriListele() {
    Ogrenci* temp = baslangic;
    if (temp == NULL) {
        printf("Listede hic ogrenci yok.\n");
        return;
    }
    printf("\nTum ogrenciler:\n");
    // Burada while dongusu bastan sona gitmesi icin degistirilmistir
    while (temp != NULL) {
        printf("Numara: %d, TC: %s, Ad: %s, Soyad: %s, Dersler: %s, Bolum: %s\n",
               temp->numara, temp->tcKimlik, temp->ad, temp->soyad, temp->dersler,temp->bolum);
        temp = temp->sonraki;  // Sonraki node'a git
    }
}

// Veri yapilari dersini alanlari listeleme fonksiyonu
void veriYapilariDersiniAlanlariListele() {
    Ogrenci* temp = baslangic;
    
    bool bulundu = false;
    printf("\nVeri Yapilari Dersi Alan ogrenciler:\n");
    while (temp != NULL) {
        if (strstr(temp->dersler, "Veri Yapilari") != NULL) {
            printf("Numara: %d, TC: %s, Ad: %s, Soyad: %s, Dersler: %s, Bolum: %s\n",
                   temp->numara, temp->tcKimlik, temp->ad, temp->soyad, temp->dersler, temp->bolum);
            bulundu = true;
        }
        temp = temp->sonraki;
    }
    if (!bulundu) {
        printf("Veri Yapilari dersini alan ogrenci bulunmamaktadir.\n");
    }
}

// ogrencileri isimlerine gore siralama fonksiyonu
void isimlereGoreSirala() {
    Ogrenci *i, *j;
    for (i = baslangic; i != NULL; i = i->sonraki) {
        for (j = i->sonraki; j != NULL; j = j->sonraki) {
            if (strcmp(i->ad, j->ad) > 0) {
                // ogrenci bilgilerini takas et
                int tempNumara = i->numara;
                i->numara = j->numara;
                j->numara = tempNumara;
                char tempTcKimlik[12];
                strcpy(tempTcKimlik, i->tcKimlik);
                strcpy(i->tcKimlik, j->tcKimlik);
                strcpy(j->tcKimlik, tempTcKimlik);
                char tempAd[50];
                strcpy(tempAd, i->ad);
                strcpy(i->ad, j->ad);
                strcpy(j->ad, tempAd);
                char tempSoyad[50];
                strcpy(tempSoyad, i->soyad);
                strcpy(i->soyad, j->soyad);
                strcpy(j->soyad, tempSoyad);
                char tempDersler[100];
                strcpy(tempDersler, i->dersler);
                strcpy(i->dersler, j->dersler);
                strcpy(j->dersler, tempDersler);
                char tempBolum[50];
                strcpy(tempBolum, i->bolum);
                strcpy(i->bolum, j->bolum);
                strcpy(j->bolum, tempBolum);
            }
        }
    }
    printf("\nOgrenciler isimlerine gore siralandi.\n");
}

// Bagli liste sonunu bulan fonksiyon
Ogrenci* sonOgrenci(Ogrenci* bas) {
    Ogrenci* temp = bas;
    while (temp != NULL && temp->sonraki != NULL) {
        temp = temp->sonraki;
    }
    return temp;
}

// Bolumleme fonksiyonu (Quick Sort icin)
Ogrenci* partition(Ogrenci* bas, Ogrenci* son, Ogrenci** newBas, Ogrenci** newSon) {
    Ogrenci* pivot = son;
    Ogrenci* prev = pivot->onceki;
    Ogrenci* current = bas;
    Ogrenci* tail = pivot;

    // Yeni baslangic ve sonu isaret etme
    *newBas = pivot;
    *newSon = pivot;

    // Listeyi pivot etrafinda duzenleme
    while (current != pivot) {
        if (current->numara < pivot->numara) {
            // Mevcut dugumu kuyrugun onune tasi
            if (*newBas == pivot) {
                *newBas = current;
            }

            prev = current;
            current = current->sonraki;
        } else {
            // Mevcut dugumu kuyrugun arkasina tasima
            if (prev) {
                prev->sonraki = current->sonraki;
            }
            Ogrenci* temp = current->sonraki;
            current->sonraki = NULL;
            tail->sonraki = current;
            current->onceki = tail;
            tail = current;
            current = temp;
        }
    }

    // Yeni sonu guncelle
    *newSon = tail;

    return pivot;
}

Ogrenci* getLast(Ogrenci* node) {
    while (node && node->sonraki) {
        node = node->sonraki;
    }
    return node;
}

// Quick Sort fonksiyonu (bagli liste icin)
void quickSortList(Ogrenci* bas, Ogrenci* son) {
    if (!bas || bas == son || bas == son->sonraki) {
        return;
    }

    Ogrenci *newBas = NULL, *newSon = NULL;
    Ogrenci* pivot = partition(bas, son, &newBas, &newSon);

    // Pivot'un sol tarafina sirala
    if (pivot != newBas) {
        Ogrenci* temp = pivot->onceki;
        if (temp) temp->sonraki = NULL;
        pivot->onceki = NULL;
        quickSortList(newBas, temp);
        // Soldan dondukten sonra, pivot'u tekrar bagla
        temp = getLast(newBas); // listenin sonunu bul
        temp->sonraki = pivot;
        pivot->onceki = temp;
    }

    // Pivot'un sag tarafina sirala
    if (pivot != newSon) {
        Ogrenci* temp = pivot->sonraki;
        if (temp) temp->onceki = NULL;
        pivot->sonraki = NULL;
        quickSortList(temp, newSon);
        // Sagdan dondukten sonra, pivot'u tekrar bagla
        pivot->sonraki = temp;
        if (temp) temp->onceki = pivot;
    }
}


bool dersiAldiMi(const char* dersler, const char* hedefDers) {
    char kopya[256];
    strcpy(kopya, dersler);
    char* token = strtok(kopya, ",");
    while (token != NULL) {
        // Bastaki ve sondaki bosluklari temizle
        while (*token == ' ') token++;
        char* end = token + strlen(token) - 1;
        while (end > token && (*end == ' ' || *end == '\n')) *end-- = '\0';

        if (strcmp(token, hedefDers) == 0) return true;
        token = strtok(NULL, ",");
    }
    return false;
}

// Veri yapilari ve algoritma analizi derslerini alanlari sirala ve listele
void veriYapilariVeAlgoritmaAnaliziDersiniAlanlariSiralaListele() {
    Ogrenci* temp = baslangic;
    Ogrenci* baslangicFiltreli = NULL;
    Ogrenci* sonFiltreli = NULL;

    // Filtreleme
    while (temp != NULL) {
        if (dersiAldiMi(temp->dersler, "Veri Yapilari") && dersiAldiMi(temp->dersler, "Algoritma Analizi")) {
            Ogrenci* yeniOgr = yeniOgrenciOlustur(temp->numara, temp->tcKimlik, temp->ad, temp->soyad, temp->dersler, temp->bolum);
            yeniOgr->onceki = NULL;
            yeniOgr->sonraki = NULL;

            if (baslangicFiltreli == NULL) {
                baslangicFiltreli = yeniOgr;
                sonFiltreli = yeniOgr;
            } else {
                sonFiltreli->sonraki = yeniOgr;
                yeniOgr->onceki = sonFiltreli;
                sonFiltreli = yeniOgr;
            }
        }
        temp = temp->sonraki;
    }

    // Filtre bossa
    if (baslangicFiltreli == NULL) {
        printf("\nVeri Yapilari ve Algoritma Analizi derslerini alan ogrenci bulunmamaktadir.\n");
        return;
    }

    // Siralama
    quickSortList(baslangicFiltreli, sonFiltreli);

    // Yazdirma
    printf("\nVeri Yapilari ve Algoritma Analizi Dersi Alan Ogrenciler (Numaraya Gore Sirali):\n");
    Ogrenci* yazdirTemp = baslangicFiltreli;
    while (yazdirTemp != NULL) {
        printf("Numara: %d, TC: %s, Ad: %s, Soyad: %s, Dersler: %s, Bolum: %s\n",
               yazdirTemp->numara, yazdirTemp->tcKimlik, yazdirTemp->ad, yazdirTemp->soyad,
               yazdirTemp->dersler, yazdirTemp->bolum);
        yazdirTemp = yazdirTemp->sonraki;
    }

    // Hafiza temizligi
    Ogrenci* current = baslangicFiltreli;
    while (current != NULL) {
        Ogrenci* next = current->sonraki;
        free(current);
        current = next;
    }
}
int main() {
    ogrenciEkle(1, "12345678901", "Ahmet", "Kara", "Veri Yapilari, Matematik", "Bilgisayar Muhendisligi");
    ogrenciEkle(2, "10987654321", "Mehmet", "Yilmaz", "Veri Yapilari, Kimya", "Elektrik Muhendisligi");
    ogrenciEkle(3, "23456789012", "Ayse", "Ozturk", "Algoritmalar, Matematik", "Makine Muhendisligi");
    ogrenciEkle(4, "34567890123", "Zeynep", "Buyuk", "Veri Yapilari", "Elektrik Muhendisligi");
    ogrenciEkle(5, "45678901234", "Fatma", "Koksal", "Veri Yapilari, Matematik", "Bilgisayar Muhendisligi");
    ogrenciEkle(6, "56789012345", "Ali", "Can", "Veri Yapilari,Algoritma Analizi,Matematik", "Bilgisayar Muhendisligi");
    ogrenciEkle(7, "67890123456", "Elif", "Demir", "Kimya, Matematik", "Yazilim Muhendisligi");
    ogrenciEkle(8, "78901234567", "Can", "Aydin", "Algoritma Analizi, Fizik", "Bilgisayar Muhendisligi");
    ogrenciEkle(9, "89012345678", "Selin", "Ersoy", "Veri Yapilari", "Endustri Muhendisligi");
    ogrenciEkle(10,"90123456789", "Emre", "Guler", "Veri Yapilari,Algoritma Analizi", "Bilgisayar Muhendisligi");
    ogrenciEkle(11,"91123456789", "Ismail","Yilmaz","Kimya,Fizik","Makine Muhendisligi");
    ogrenciEkle(12,"92123456789", "Yilmaz","Sahin","Algoritmalar,Kimya","Elektrik Muhendisligi");
    ogrenciEkle(13,"93123456789", "Merve","Ozcan","Fizik,Kimya","Endustri Muhendisligi");
	ogrenciEkle(14,"94123456789", "Dilan","Sahin","Matematik,Fizik","Makine Muhendisligi");
	ogrenciEkle(15,"95123456789", "Serhat","Sari","Veri Yapilari,Algoritma Analizi","Yazilim Muhendisligi");
	ogrenciEkle(16,"96123456789", "Beyza","Aksu","Algoritmalar,Fizik","Endustri Muhendisligi");
	ogrenciEkle(17,"97123456789", "Merve","Ozcan","Matematik,Fizik","Elektrik Muhendisligi");
	ogrenciEkle(18,"98123456789", "Aleyna","Kilic","Veri Yapilari,Matematik","Yazilim Muhendisligi");
	ogrenciEkle(19,"99123456789", "Furkan","Salman","Matematik,Kimya","Makine Muhendisligi");
	ogrenciEkle(20,"88123456789", "Selen","Ozder","Algoritmalar,Algoritma Analizi","Bilgisayar Muhendisligi");
	ogrenciEkle(21,"87123456789", "Yunus","Engin","Matematik,Algoritma Analizi","Bilgisayar Muhendisligi");
	ogrenciEkle(22,"86123456789", "Umit","Ozcan","Algoritmalar,Kimya","Endustri Muhendisligi");
	ogrenciEkle(23,"85123456789", "Sema","Demir","Veri Yapilari","Bilgisayar Muhendisligi");
	ogrenciEkle(24,"84123456789", "Batuhan","Sen","Algoritmalar","Yazilim Muhendisligi");
	ogrenciEkle(25,"83123456789", "Pelin","Gurel","Algoritmalar","Bilgisayar Muhendisligi");
	
    // (Daha fazla ogrenci eklenebilir)

    int secim;
    do {
        printf("\nMenu:\n");
        printf("1. Tum ogrencileri listele\n");
        printf("2. Veri yapilari dersini alanlari listele\n");
        printf("3. Isimlere gore sirala\n");
        printf("4. Quick sort ile veri yapilari ve algoritma analizi derslerini alanlari sirala\n");
        printf("5. Cikis\n");
        printf("Seciminizi yapin: ");
        scanf("%d", &secim);

        switch (secim) {
            case 1:
                ogrencileriListele();
                break;
            case 2:
                veriYapilariDersiniAlanlariListele();
                break;
            case 3:
                isimlereGoreSirala();
                ogrencileriListele();
                break;
            case 4:
                veriYapilariVeAlgoritmaAnaliziDersiniAlanlariSiralaListele();
                break;
            case 5:
                printf("Programdan cikiliyor...\n");
                break;
            default:
                printf("Gecersiz secim! Tekrar deneyin.\n");
        }
    } while (secim != 5);

    // Program sonlanirken listeyi temizle
    Ogrenci* current = baslangic;
    while (current != NULL) {
        Ogrenci* next = current->sonraki;
        free(current);
        current = next;
    }
    baslangic = NULL;

    return 0;
}

