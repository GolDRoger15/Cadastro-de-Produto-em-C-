# Cadastro-de-Produto-em-C-
Criei um programa com o objetivo de armazenar o produto, com as opçoes de 1-Cadastrar tipo; 2-Cadastrar o produto; 3- Consultar o preço; 4- Excluir tipo. Eu utilizei duas estruturas de dados do tipo fila para armazenar os produtos. 

#include <stdio.h>
#include <stdlib.h>

// Definição da estrutura Tipo
struct Tipo {
    char letra;
    float percentual_imposto;
};

// Definição da estrutura Produto
struct Produto {
    int numero;
    float preco;
    char tipo;
};

// Definição da estrutura FilaTipo
struct FilaTipo {
    struct Tipo *tipos;
    int tamanho;
    int capacidade;
};

// Definição da estrutura FilaProduto
struct FilaProduto {
    struct Produto *produtos;
    int tamanho;
    int capacidade;
    int ultimo_numero;
};

// Função para criar uma fila de tipos
struct FilaTipo *criarFilaTipo(int capacidade) {
    struct FilaTipo *fila = (struct FilaTipo *)malloc(sizeof(struct FilaTipo));
    fila->tipos = (struct Tipo *)malloc(capacidade * sizeof(struct Tipo));
    fila->tamanho = 0;
    fila->capacidade = capacidade;
    return fila;
}

// Função para criar uma fila de produtos
struct FilaProduto *criarFilaProduto(int capacidade) {
    struct FilaProduto *fila = (struct FilaProduto *)malloc(sizeof(struct FilaProduto));
    fila->produtos = (struct Produto *)malloc(capacidade * sizeof(struct Produto));
    fila->tamanho = 0;
    fila->capacidade = capacidade;
    fila->ultimo_numero = 0;
    return fila;
}

// Função para enfileirar um tipo
void enfileirarTipo(struct FilaTipo *fila, struct Tipo novo_tipo) {
    if (fila->tamanho < fila->capacidade) {
        fila->tipos[fila->tamanho++] = novo_tipo;
    }
}

// Função para enfileirar um produto
void enfileirarProduto(struct FilaProduto *fila, struct Produto novo_produto) {
    if (fila->tamanho < fila->capacidade) {
        novo_produto.numero = ++fila->ultimo_numero;
        fila->produtos[fila->tamanho++] = novo_produto;
        printf("Produto cadastrado. Número do produto: %d\n", novo_produto.numero);
    }
}

// Função para consultar o preço de um produto
void consultarPreco(struct FilaTipo *tipos, struct FilaProduto *produtos, int numero_produto) {
    if (produtos->tamanho > 0) {
        int produtoEncontrado = 0;
        float precoCalculado;

        struct Produto produto;
        struct Tipo tipo_produto;

        for (int i = 0; i < produtos->tamanho; i++) {
            if (produtos->produtos[i].numero == numero_produto) {
                produto = produtos->produtos[i];
                produtoEncontrado = 1;
                break;
            }
        }

        if (produtoEncontrado) {
            int tipoEncontrado = 0;

            for (int i = 0; i < tipos->tamanho; i++) {
                if (tipos->tipos[i].letra == produto.tipo) {
                    tipo_produto = tipos->tipos[i];
                    tipoEncontrado = 1;
                    break;
                }
            }

            if (tipoEncontrado) {
                precoCalculado = produto.preco - (produto.preco * tipo_produto.percentual_imposto / 100);
                printf("Preço = %.2f\n", precoCalculado);
            } else {
                printf("Tipo de produto não encontrado.\n");
            }
        } else {
            printf("Produto não encontrado.\n");
        }
    } else {
        printf("Fila de produtos vazia.\n");
    }
}

// Função para desenfileirar um tipo
int desenfileirarTipo(struct FilaTipo *fila, struct Tipo *tipo) {
    if (fila->tamanho > 0) {
        *tipo = fila->tipos[0];

        for (int i = 0; i < fila->tamanho - 1; i++) {
            fila->tipos[i] = fila->tipos[i + 1];
        }

        fila->tamanho--;
        return 1;
    } else {
        return 0;
    }
}

// Função para desenfileirar um produto
int desenfileirarProduto(struct FilaProduto *fila, struct Produto *produto) {
    if (fila->tamanho > 0) {
        *produto = fila->produtos[0];

        for (int i = 0; i < fila->tamanho - 1; i++) {
            fila->produtos[i] = fila->produtos[i + 1];
        }

        fila->tamanho--;
        return 1;
    } else {
        return 0;
    }
}

// Função para excluir um tipo
void excluirTipo(struct FilaTipo *tipos, struct FilaProduto *produtos, char tipo) {
    int tipoEncontrado = 0;

    struct Tipo tipo_produto;
    struct FilaTipo *tiposTemporarios = criarFilaTipo(100);

    while (desenfileirarTipo(tipos, &tipo_produto)) {
        if (tipo_produto.letra == tipo) {
            tipoEncontrado = 1;
        } else {
            enfileirarTipo(tiposTemporarios, tipo_produto);
        }
    }

    while (tiposTemporarios->tamanho > 0) {
        struct Tipo tipoAtual;
        desenfileirarTipo(tiposTemporarios, &tipoAtual);
        enfileirarTipo(tipos, tipoAtual);
    }

    free(tiposTemporarios->tipos);
    free(tiposTemporarios);

    if (tipoEncontrado) {
        struct FilaProduto *produtos_a_manter = criarFilaProduto(100);

        struct Produto produto;

        while (desenfileirarProduto(produtos, &produto)) {
            if (produto.tipo != tipo) {
                enfileirarProduto(produtos_a_manter, produto);
            }
        }

        while (produtos_a_manter->tamanho > 0) {
            struct Produto produtoAtual;
            desenfileirarProduto(produtos_a_manter, &produtoAtual);
            enfileirarProduto(produtos, produtoAtual);
        }

        free(produtos_a_manter->produtos);
        free(produtos_a_manter);

        printf("Tipo excluído.\n");
    } else {
        printf("Tipo não encontrado.\n");
    }
}

int main() {
    struct FilaTipo *tipos = criarFilaTipo(100);
    struct FilaProduto *produtos = criarFilaProduto(100);

    int opcao;

    do {
        printf("\nMENU\n");
        printf("1. Cadastrar tipo\n");
        printf("2. Cadastrar produto\n");
        printf("3. Consultar preço de um produto\n");
        printf("4. Excluir tipo\n");
        printf("5. Sair\n");

        printf("Escolha uma opção: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1: {
                struct Tipo novo_tipo;
                printf("Digite a letra que representa o tipo: ");
                scanf(" %c", &novo_tipo.letra);

                printf("Digite o percentual de imposto: ");
                scanf("%f", &novo_tipo.percentual_imposto);

                enfileirarTipo(tipos, novo_tipo);
                printf("Tipo cadastrado.\n");
                break;
            }
            case 2: {
                struct Produto novo_produto;
                printf("Digite o preço do produto: ");
                scanf("%f", &novo_produto.preco);

                printf("Digite a letra que representa o tipo do produto: ");
                scanf(" %c", &novo_produto.tipo);  // Adicionei um espaço antes de %c

                enfileirarProduto(produtos, novo_produto);
                break;
            }
            case 3: {
                int numero_produto;
                printf("Digite o número do produto: ");
                scanf("%d", &numero_produto);
                consultarPreco(tipos, produtos, numero_produto);
                break;
            }
            case 4: {
                char tipo;
                printf("Digite a letra que representa o tipo a ser excluído: ");
                scanf(" %c", &tipo);  // Adicionei um espaço antes de %c
                excluirTipo(tipos, produtos, tipo);
                break;
            }
            case 5: {
                printf("Saindo do programa.\n");
                break;
            }
            default:
                printf("Opção inválida.\n");
        }
    } while (opcao != 5);

    free(tipos->tipos);
    free(tipos);
    free(produtos->produtos);
    free(produtos);

    return 0;
}
