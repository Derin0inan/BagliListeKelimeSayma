#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <locale.h>
#include <ctype.h>
#include <stdbool.h>


struct Dugum{
    char *kelime;
    int kfrekans;
    struct Dugum* sonraki;
};


void ekle_sondan(struct Dugum** referans, char* kelime);
void ekle_bastan(struct Dugum** referans, char *kelime,int kfrekans);
void ekle_aradan(struct Dugum** referans, struct Dugum* araya_gelecek);
bool dosyaAra(struct Dugum **head, char *kelime);
void dosyaOku(FILE *file, char metin[]);

struct Dugum* deg=NULL;

int main() {

    setlocale(LC_ALL, "Turkish");

    struct Dugum* head = NULL;

    char *dosya="t.txt";
    FILE *file = fopen(dosya,"r");
    char metin[5000];

    dosyaOku(file, metin);

    char *kelime;
    kelime=strtok(metin, " \n");

    ekle_sondan(&head, kelime);

    for(;;){

        kelime = strtok(NULL," \n");
        if(kelime==NULL)
                break;

        if(isprint(kelime[strlen(kelime)-1])==0)
            kelime[strlen(kelime)-1]='\0';



        if(strcmp(head->kelime,kelime)==0)
             head->kfrekans++;

        else if(dosyaAra(&head,kelime) == true){

            if(deg->kfrekans > head->kfrekans)
                ekle_bastan(&head, deg->kelime, deg->kfrekans);
            else
                ekle_aradan(&head,deg);
        }

        else
            ekle_sondan(&head,kelime);
    }

    fclose(file);

    int sira = 0;
    while (head != NULL) {
        printf("%d. %s %d \n",sira + 1, head->kelime, head->kfrekans);
        head = head->sonraki;
        sira++;
    }

    return 0;
}


void dosyaOku(FILE *file, char metin[]){

    if(file == NULL)
        printf("Boyle bir dosya bulunamadi");

    else{
        int a = 0;
        while(!feof(file)){
            metin[a] = getc(file);
            a++;
        }
    }
}


void ekle_bastan(struct Dugum** referans, char *kelime, int kfrekans) {

    struct Dugum* bastan_dugum = (struct Dugum*) malloc(sizeof(struct Dugum));

    bastan_dugum->kelime  = kelime;
    bastan_dugum->kfrekans  = kfrekans;

    bastan_dugum->sonraki = *referans;
    *referans = bastan_dugum;
}


void ekle_aradan(struct Dugum** referans, struct Dugum* araya_gelecek) {

    struct Dugum* gecici = *referans;
    struct Dugum* aradan_dugum = gecici->sonraki;

    while(aradan_dugum != NULL){

        if(araya_gelecek->kfrekans > aradan_dugum->kfrekans){
            araya_gelecek->sonraki = aradan_dugum;
            gecici->sonraki = araya_gelecek;
            break;
        }

        gecici = aradan_dugum;
        aradan_dugum = aradan_dugum->sonraki;
    }
}

void ekle_sondan(struct Dugum** referans, char* kelime) {

    struct Dugum* yeni_dugum = (struct Dugum*)malloc(sizeof(struct Dugum));
    struct Dugum* son = *referans;

    yeni_dugum->kelime= kelime;
    yeni_dugum->kfrekans= 1;
    yeni_dugum->sonraki = NULL;

    if (*referans == NULL) {
        *referans = yeni_dugum;
        return;
    }

    while (son->sonraki != NULL)
        son = son->sonraki;

    son->sonraki = yeni_dugum;
    return;

}


bool dosyaAra(struct Dugum **head, char *kelime){

        struct Dugum* gecici = NULL;
        struct Dugum* gecici_2 = *head;

        gecici = gecici_2;
        gecici = gecici->sonraki;

        while (gecici != NULL){

            if (strcmp(gecici->kelime, kelime)==0){
                gecici->kfrekans++;
                gecici_2->sonraki = gecici->sonraki;
                deg = gecici;
                return true;
            }

            gecici_2 = gecici;
            gecici = gecici->sonraki;
        }

    return false;
}
