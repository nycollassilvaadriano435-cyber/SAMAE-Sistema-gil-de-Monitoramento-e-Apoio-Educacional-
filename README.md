Área de Código em C

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define QTD_MATERIAS 4
#define QTD_NOTAS 3
#define AULAS_TOTAL 20

//Estrutura das matérias

struct Materia {
    char nome[40];
    float notas[QTD_NOTAS];
    float media;
    int faltas;
    float frequencia;
    char status[40];
};

// Estrutura do aluno

struct Aluno {
    char nome[50];
    char RA[20];
    char senha[20];
    struct Materia materias[QTD_MATERIAS];
    float mediaGeral;
};

// Função de login

int login(struct Aluno alunos[], int qtd) {
    char raLogin[20];
    char senhaLogin[20];
    int tentativas = 3;

    while (tentativas > 0) {
        printf("\n-----LOGIN-----\n");
        printf("RA: ");
        scanf(" %s", raLogin);
        printf("Senha: ");
        scanf(" %s", senhaLogin);
        
            // strcmp realizado para a comparação entre strings 
            
        for (int i = 0; i < qtd; i++) {
            if (strcmp(raLogin, alunos[i].RA) == 0 && strcmp(senhaLogin, alunos[i].senha) == 0) {
                printf("\nLogin efetuado!\n");
                printf("Bem-vindo(a), %s!\n", alunos[i].nome);
                return i; // retorna o índice do aluno logado
            }
        }

        tentativas--;
        printf("\nRA ou senha incorretos! Tentativas restantes: %d\n", tentativas);
    }

    printf("\nNúmero máximo de tentativas. Encerrando o programa.\n");
    return -1;
}

int main() {
    int qtdAlunos;
    char nomesMaterias[QTD_MATERIAS][30] = {
        "Eng de software ágil",
        "Python",
        "Prog est em C",
        "Análise e proj de sistemas"
    };
    
    FILE *arquivo;

    printf("-----SAMAE-----\n");
    printf("Quantos alunos serão cadastrados?\n");
    scanf("%d", &qtdAlunos);

    struct Aluno alunos[qtdAlunos];

    printf("\n-----CADASTRO DOS ALUNOS-----\n\n");

    for (int i = 0; i < qtdAlunos; i++) {
        printf("Aluno %d\n", i + 1);

        printf("Nome: ");
        scanf(" %[^\n]", alunos[i].nome);
        
        // Leia tudo até o \n
        
        printf("RA: ");
        scanf(" %s", alunos[i].RA);

        printf("Senha: ");
        scanf(" %s", alunos[i].senha);

        float somaMedias = 0.0;
            // strcpy realizado para a cópia de uma string para outra
        for (int m = 0; m < QTD_MATERIAS; m++) {
            strcpy(alunos[i].materias[m].nome, nomesMaterias[m]);
            printf("\n%s\n", alunos[i].materias[m].nome);

            float somaNotas = 0.0;
            for (int n = 0; n < QTD_NOTAS; n++) {
                printf("Nota %d: ", n + 1);
                scanf("%f", &alunos[i].materias[m].notas[n]);
                somaNotas += alunos[i].materias[m].notas[n];
            }

            alunos[i].materias[m].media = somaNotas / QTD_NOTAS;

            printf("Quantidade de faltas na matéria (0 a 10): ");
            scanf("%d", &alunos[i].materias[m].faltas);

            // Frequência
            
            alunos[i].materias[m].frequencia =
                ((float)(AULAS_TOTAL - alunos[i].materias[m].faltas) / AULAS_TOTAL) * 100;

            // Verificação de status
            
            if (alunos[i].materias[m].frequencia < 75) {
                strcpy(alunos[i].materias[m].status, "Reprovado por falta");
            } else if (alunos[i].materias[m].media >= 7) {
                strcpy(alunos[i].materias[m].status, "Aprovado");
            } else if (alunos[i].materias[m].media < 7) {
                strcpy(alunos[i].materias[m].status, "Recuperação");
                // strcpy realizado para a cópia de uma string para outra
                printf("  %s está em recuperação. Digite a nota da recuperação: ",
                       alunos[i].materias[m].nome);
                float notaRec;
                scanf("%f", &notaRec);

                float novaMedia = (alunos[i].materias[m].media + notaRec) / 2.0;

                if (novaMedia >= 5) {
                    strcpy(alunos[i].materias[m].status, "Aprovado (Recuperação)");
                    alunos[i].materias[m].media = novaMedia;
                } else {
                    strcpy(alunos[i].materias[m].status, "Reprovado");
                    alunos[i].materias[m].media = novaMedia;
                }
            } else {
                strcpy(alunos[i].materias[m].status, "Reprovado");
            }

            somaMedias += alunos[i].materias[m].media;
        }

        alunos[i].mediaGeral = somaMedias / QTD_MATERIAS;
        printf("\nMédia geral do aluno: %.2f\n", alunos[i].mediaGeral);
    }

    // Criação do arquivo
    
    arquivo = fopen("alunos.txt", "w");
    if (arquivo == NULL) {
        printf("Erro ao criar o arquivo!\n");
        return 1;
    }

    fprintf(arquivo, "Nome\tRA\tSenha\t");
    for (int m = 0; m < QTD_MATERIAS; m++) {
        fprintf(arquivo, "%s (média/freq/status)\t", nomesMaterias[m]);
    }
    fprintf(arquivo, "Média Geral\n");
    fprintf(arquivo, "-----------------------------------------------------------------------\n");

    for (int i = 0; i < qtdAlunos; i++) {
        fprintf(arquivo, "%s\t%s\t%s\t", alunos[i].nome, alunos[i].RA, alunos[i].senha);
        for (int m = 0; m < QTD_MATERIAS; m++) {
            fprintf(arquivo, "%.2f (%.0f%% / %s)\t",
                alunos[i].materias[m].media,
                alunos[i].materias[m].frequencia,
                alunos[i].materias[m].status);
        }
        fprintf(arquivo, "%.2f\n", alunos[i].mediaGeral);
    }

    fclose(arquivo);
    printf("\nArquivo 'alunos.txt' criado com sucesso!\n");

    // Limpar o terminal
    
#ifdef _WIN32
    system("cls");
#else
    system("clear");
#endif

    int indiceLogado = login(alunos, qtdAlunos);
    if (indiceLogado == -1) {
        return 1;
    }

    // Exibe boletim do aluno logado
    
    printf("\n-----BOLETIM DO ALUNO-----\n");
    printf("Nome: %s\nRA: %s\n", alunos[indiceLogado].nome, alunos[indiceLogado].RA);

    for (int m = 0; m < QTD_MATERIAS; m++) {
        printf("%s - Média: %.2f - Freq: %.0f%% - Status: %s\n",
               alunos[indiceLogado].materias[m].nome,
               alunos[indiceLogado].materias[m].media,
               alunos[indiceLogado].materias[m].frequencia,
               alunos[indiceLogado].materias[m].status);
    }

    printf("\nMédia geral: %.2f\n", alunos[indiceLogado].mediaGeral);

    return 0;
}
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Área de Código em Python

import os
import getpass

def carregar_alunos():
    alunos = {}
    try:
        with open("alunos.txt", "r", encoding="utf-8") as f:
            linhas = f.readlines()[2:]  # pula cabeçalho e linha separadora
            for linha in linhas:
                partes = linha.strip().split("\t")
                if len(partes) < 8:
                    continue  # ignora linhas incompletas

                nome = partes[0]
                ra = partes[1]
                senha = partes[2]
                materias = {
                    "Engenharia de Software Ágil": partes[3],
                    "Python": partes[4],
                    "Programação Estruturada em C": partes[5],
                    "Análise e Projeto de Sistemas": partes[6]
                }
                media_geral = partes[7]

                alunos[ra] = {
                    "nome": nome,
                    "senha": senha,
                    "materias": materias,
                    "media_geral": media_geral
                }

    except FileNotFoundError:
        print("Erro: o arquivo 'alunos.txt' não foi encontrado. Execute primeiro o programa em C.")
        exit()

    return alunos


def mostrar_materia(aluno, materia):
    dados = aluno["materias"][materia]
    print(f"\n {materia}")
    print(f"Nota: {dados}")
    print("-------------------------------")


def chatbot():
    alunos = carregar_alunos() 

    if not alunos:
        print("Nenhum aluno foi carregado. Verifique o arquivo 'alunos.txt'.")
        return

    print("Olá! Bem-vindo ao SAMAE (Sistema Ágil de Monitoramento e Apoio Educacional)\n")

    ra = input("Digite seu RA para acessar: ").strip()

    if ra not in alunos:
        print("RA não encontrado. Certifique-se de que foi cadastrado pelo sistema C.")
        return

    aluno = alunos[ra]

    #  Solicita senha sem exibi-la no terminal
    senha_digitada = getpass.getpass("Digite sua senha: ")

    if senha_digitada != aluno["senha"]:
        print("Senha incorreta. Acesso negado.")
        return

    print(f"\n Acesso liberado. Bem-vindo(a), {aluno['nome']}!")

    while True:
        print("\nQual matéria você deseja consultar?")
        print("[1] Engenharia de Software Ágil")
        print("[2] Python")
        print("[3] Programação Estruturada em C")
        print("[4] Análise e Projeto de Sistemas")
        print("[5] Ver média geral")
        print("[6] Encerrar")

        escolha = input("> ")

        if escolha == "1":
            mostrar_materia(aluno, "Engenharia de Software Ágil")
        elif escolha == "2":
            mostrar_materia(aluno, "Python")
        elif escolha == "3":
            mostrar_materia(aluno, "Programação Estruturada em C")
        elif escolha == "4":
            mostrar_materia(aluno, "Análise e Projeto de Sistemas")
        elif escolha == "5":
            print(f"\n Média geral: {aluno['media_geral']}")
        elif escolha == "6":
            print("\nEncerrando o atendimento. Conte sempre com a SAMAE!")
            break
        else:
            print("Opção inválida. Digite de 1 a 6.")
            
        if __name__ == "__main__":
    chatbot()


