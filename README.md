#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

// ==================== ESTRUTURAS ====================

typedef struct {
    char nome[50];
    char cor[20];
    int tropas;
} Jogador;

typedef struct {
    char nome[50];
    Jogador *dono;   // ponteiro para o jogador dono
    int tropas;
} Territorio;

typedef struct {
    char descricao[100];
    int concluida;
} Missao;

// ==================== FUNÇÕES ====================

void cadastrarJogadores(Jogador *jogadores, int qtd) {
    for (int i = 0; i < qtd; i++) {
        printf("\n--- Cadastro do Jogador %d ---\n", i + 1);
        printf("Nome: ");
        scanf(" %[^\n]", jogadores[i].nome);
        printf("Cor: ");
        scanf(" %[^\n]", jogadores[i].cor);
        jogadores[i].tropas = 10; // valor inicial
    }
}

void mostrarJogadores(Jogador *jogadores, int qtd) {
    printf("\n==== Jogadores ====\n");
    for (int i = 0; i < qtd; i++) {
        printf("%d. %s (%s) - Tropas: %d\n", i + 1, jogadores[i].nome, jogadores[i].cor, jogadores[i].tropas);
    }
}

void inicializarTerritorios(Territorio *territorios, int qtd, Jogador *jogadores, int qtdJog) {
    for (int i = 0; i < qtd; i++) {
        sprintf(territorios[i].nome, "Territorio %d", i + 1);
        territorios[i].dono = &jogadores[i % qtdJog];
        territorios[i].tropas = 5;
    }
}

void mostrarTerritorios(Territorio *territorios, int qtd) {
    printf("\n==== Territórios ====\n");
    for (int i = 0; i < qtd; i++) {
        printf("%s - Dono: %s - Tropas: %d\n", territorios[i].nome, territorios[i].dono->nome, territorios[i].tropas);
    }
}

// Simula uma batalha entre dois territórios
void atacar(Territorio *atacante, Territorio *defensor) {
    if (atacante->dono == defensor->dono) {
        printf("Não é possível atacar seu próprio território!\n");
        return;
    }
    if (atacante->tropas < 2) {
        printf("Tropas insuficientes para atacar!\n");
        return;
    }

    int ataque = rand() % atacante->tropas + 1;
    int defesa = rand() % defensor->tropas + 1;

    printf("\n%s ataca %s!\n", atacante->nome, defensor->nome);
    printf("Ataque: %d | Defesa: %d\n", ataque, defesa);

    if (ataque > defesa) {
        printf("%s conquistou o território!\n", atacante->dono->nome);
        defensor->dono = atacante->dono;
        defensor->tropas = atacante->tropas / 2;
        atacante->tropas /= 2;
    } else {
        printf("Defesa bem-sucedida!\n");
        atacante->tropas /= 2;
    }
}

void atribuirMissoes(Missao *missoes, int qtd) {
    const char *descricoes[] = {
        "Conquistar 3 territórios",
        "Manter 2 territórios sem perder nenhum turno",
        "Eliminar um jogador adversário"
    };

    for (int i = 0; i < qtd; i++) {
        strcpy(missoes[i].descricao, descricoes[rand() % 3]);
        missoes[i].concluida = 0;
    }
}

void verificarVitoria(Missao *missoes, Jogador *jogadores, int qtd) {
    for (int i = 0; i < qtd; i++) {
        if (missoes[i].concluida) {
            printf("\n🏆 O jogador %s venceu! Missão: %s\n", jogadores[i].nome, missoes[i].descricao);
            exit(0);
        }
    }
}

// ==================== PROGRAMA PRINCIPAL ====================

int main() {
    srand(time(NULL));

    int qtdJog, qtdTerr = 6;

    printf("Quantos jogadores? ");
    scanf("%d", &qtdJog);

    // Alocação dinâmica
    Jogador *jogadores = malloc(qtdJog * sizeof(Jogador));
    Territorio *territorios = malloc(qtdTerr * sizeof(Territorio));
    Missao *missoes = malloc(qtdJog * sizeof(Missao));

    cadastrarJogadores(jogadores, qtdJog);
    inicializarTerritorios(territorios, qtdTerr, jogadores, qtdJog);
    atribuirMissoes(missoes, qtdJog);

    int opc;
    do {
        printf("\n=== MENU ===\n");
        printf("1. Mostrar jogadores\n");
        printf("2. Mostrar territórios\n");
        printf("3. Atacar\n");
        printf("4. Sair\n");
        printf("Escolha: ");
        scanf("%d", &opc);

        if (opc == 1)
            mostrarJogadores(jogadores, qtdJog);
        else if (opc == 2)
            mostrarTerritorios(territorios, qtdTerr);
        else if (opc == 3) {
            int a, d;
            mostrarTerritorios(territorios, qtdTerr);
            printf("Escolha território atacante (1-%d): ", qtdTerr);
            scanf("%d", &a);
            printf("Escolha território defensor (1-%d): ", qtdTerr);
            scanf("%d", &d);
            atacar(&territorios[a - 1], &territorios[d - 1]);
        }
    } while (opc != 4);

    free(jogadores);
    free(territorios);
    free(missoes);

    printf("\nJogo encerrado!\n");
    return 0;
}
