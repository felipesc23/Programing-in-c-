# Programing-in-c-
Some of my c programs
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_CLIENTES 100

struct Cliente
{
    char nome[30];
    char CPF[12];
    char endereco[30];
    char bairro[30];
    char dataNascimento[11];
    char dataUltimaCompra[11];
    float valorAcumuladoCompras;
};

void cadastrarCliente(FILE *arquivo)
{
    struct Cliente cliente;

    printf("=== Cadastrar Cliente ===\n");

    printf("Nome: ");
    fgets(cliente.nome, sizeof(cliente.nome), stdin);
    cliente.nome[strcspn(cliente.nome, "\n")] = '\0';

    printf("CPF: ");
    fgets(cliente.CPF, sizeof(cliente.CPF), stdin);
    cliente.CPF[strcspn(cliente.CPF, "\n")] = '\0';

    printf("Endereco: ");
    fgets(cliente.endereco, sizeof(cliente.endereco), stdin);
    cliente.endereco[strcspn(cliente.endereco, "\n")] = '\0';

    printf("Bairro: ");
    fgets(cliente.bairro, sizeof(cliente.bairro), stdin);
    cliente.bairro[strcspn(cliente.bairro, "\n")] = '\0';

    printf("Data de Nascimento: ");
    fgets(cliente.dataNascimento, sizeof(cliente.dataNascimento), stdin);
    cliente.dataNascimento[strcspn(cliente.dataNascimento, "\n")] = '\0';

    printf("Data da Ultima Compra: ");
    fgets(cliente.dataUltimaCompra, sizeof(cliente.dataUltimaCompra), stdin);
    cliente.dataUltimaCompra[strcspn(cliente.dataUltimaCompra, "\n")] = '\0';

    printf("Valor Acumulado de Compras: ");
    scanf("%f", &cliente.valorAcumuladoCompras);
    getchar(); // Consumir o caractere de nova linha residual

    fprintf(arquivo, "%s;%s;%s;%s;%s;%s;%.2f\n", cliente.nome, cliente.CPF, cliente.endereco, cliente.bairro, cliente.dataNascimento, cliente.dataUltimaCompra, cliente.valorAcumuladoCompras);

    printf("Cliente cadastrado com sucesso!\n");
}

void listarClienteUltimaCompra(FILE *arquivo)
{
    char data[11];
    printf("=== Listar Cliente por Data da Ultima Compra ===\n");
    printf("Digite a data da última compra (dd/mm/aaaa): ");
    fgets(data, sizeof(data), stdin);
    data[strcspn(data, "\n")] = '\0';

    char linha[256];
    char *token;

    printf("CPF\t\tNome\t\tEndereco\n");

    while (fgets(linha, sizeof(linha), arquivo))
    {
        token = strtok(linha, ";");                 // Nome
        token = strtok(NULL, ";");                  // CPF
        token = strtok(NULL, ";");                  // Endereco
        token = strtok(NULL, ";");                  // Bairro
        token = strtok(NULL, ";");                  // Data de Nascimento
        char *dataUltimaCompra = strtok(NULL, ";"); // Data da Ultima Compra
        token = strtok(NULL, ";");                  // Valor Acumulado de Compras

        if (strcmp(data, dataUltimaCompra) == 0)
        {
            printf("%s\t%s\t%s\n", token, strtok(NULL, ";"), strtok(NULL, ";"));
        }
    }
}

void contarClientesBairro(FILE *arquivo)
{
    char bairro[30];
    printf("=== Contar Clientes por Bairro ===\n");
    printf("Digite o nome do bairro: ");
    fgets(bairro, sizeof(bairro), stdin);
    bairro[strcspn(bairro, "\n")] = '\0';

    char linha[256];
    char *token;
    int contador = 0;

    while (fgets(linha, sizeof(linha), arquivo))
    {
        token = strtok(linha, ";");              // Nome
        token = strtok(NULL, ";");               // CPF
        token = strtok(NULL, ";");               // Endereco
        char *bairroCliente = strtok(NULL, ";"); // Bairro
        token = strtok(NULL, ";");               // Data de Nascimento
        token = strtok(NULL, ";");               // Data da Ultima Compra
        token = strtok(NULL, ";");               // Valor Acumulado de Compras

        if (strcmp(bairro, bairroCliente) == 0)
        {
            contador++;
        }
    }

    printf("Número de clientes no bairro %s: %d\n", bairro, contador);
}

void listarClienteMaiorValor(FILE *arquivo)
{
    printf("=== Listar Cliente com Maior Valor Acumulado de Compras ===\n");

    char linha[256];
    char *token;
    char maiorCliente[30];
    float maiorValor = 0.0;

    while (fgets(linha, sizeof(linha), arquivo))
    {
        token = strtok(linha, ";");            // Nome
        token = strtok(NULL, ";");             // CPF
        token = strtok(NULL, ";");             // Endereco
        token = strtok(NULL, ";");             // Bairro
        token = strtok(NULL, ";");             // Data de Nascimento
        token = strtok(NULL, ";");             // Data da Ultima Compra
        float valor = atof(strtok(NULL, ";")); // Valor Acumulado de Compras

        if (valor > maiorValor)
        {
            maiorValor = valor;
            strcpy(maiorCliente, token);
        }
    }

    printf("Cliente com maior valor acumulado de compras: %s\n", maiorCliente);
}

int main()
{
    FILE *arquivo;
    arquivo = fopen("clientes.txt", "a+");

    if (arquivo == NULL)
    {
        printf("Erro ao abrir o arquivo.\n");
        return 1;
    }

    int opcao;
    do
    {
        printf("=== Menu ===\n");
        printf("1 - Cadastrar cliente\n");
        printf("2 - Listar cliente que fez a última compra em uma data informada pelo teclado\n");
        printf("3 - Contar quantos clientes moram em um bairro informado pelo teclado\n");
        printf("4 - Listar o nome do cliente que tem o maior valor acumulado de compras\n");
        printf("5 - Fim\n");
        printf("Digite sua opção: ");
        scanf("%d", &opcao);
        getchar(); // Consumir o caractere de nova linha residual

        switch (opcao)
        {
        case 1:
            cadastrarCliente(arquivo);
            break;
        case 2:
            listarClienteUltimaCompra(arquivo);
            break;
        case 3:
            contarClientesBairro(arquivo);
            break;
        case 4:
            listarClienteMaiorValor(arquivo);
            break;
        case 5:
            printf("Encerrando o programa.\n");
            break;
        default:
            printf("Opção inválida. Tente novamente.\n");
        }
    } while (opcao != 5);

    fclose(arquivo);
    return 0;
}
