#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define MAX_FILMES 10
#define MAX_HORARIOS 5

int valorIngresso = 40;

typedef struct Reserva{
    int numeracao;
    char poltrona[2];
    struct Reserva *proximo;
}Reserva;

typedef struct{
    int codigopedido;
    int inteiras;
    int meias;
    int dia;
    int codigofilme;
    char data[11];
    char **poltronas;
    int numPoltronas;
}pedido;

typedef struct{
    char nome[100];
    char duracao[100];
    char horarios[MAX_HORARIOS][10];
}Filme;

//função para mostrar o menu
void exibirMenuPrincipal(){
    printf("\n\033[1;34mBem-vindo ao Cineminha!\033[0m\n");
    printf("------------------------\n");
    printf("\033[1;32m1 - Comprar Ingresso\033[0m\n");
    printf("\033[1;32m2 - Checar Pedido\033[0m\n");
    printf("\033[1;31m0 - Sair\033[0m\n");
    printf("------------------------\n");
    printf("\033[1mEscolha uma opção: \033[0m");
}

//função para exibir filmes
void exibirFilmesComDetalhes(Filme filmes[]){
    printf("\n\033[1;36mFilmes em Cartaz:\033[0m\n");
    printf("------------------------\n");
    
    for(int i = 0; i < MAX_FILMES; i++){
        printf("\033[1m%d. %s\033[0m \n  - Duração: %s\n", i + 1, filmes[i].nome, filmes[i].duracao);
    }
    
    printf("------------------------\n");
}

//função horários
void exibirHorariosComNumeracao(Filme filmes[], int filmeEscolhido){
    printf("\n\033[1;36mHorários disponíveis para %s:\033[0m\n",
         filmes[filmeEscolhido - 1].nome);
    printf("------------------------\n");
    
    for(int i = 0; i < MAX_HORARIOS; i++){
        printf("\033[1m%d.\033[0m %s\n", i + 1, filmes[filmeEscolhido - 1].horarios[i]);
    }
    
    printf("------------------------\n");
}

//funçao para mostrar os pedidos confirmados
void exibirConfirmacaoPedido(pedido pedidoAtual, Filme filmes[], int horarioEscolhido){
    printf("\n\033[1;32mINGRESSOS RESERVADOS COM SUCESSO!\033[0m\n");
    printf("------------------------\n");
    printf("Número do Pedido: \033[1m%d\033[0m\n", pedidoAtual.codigopedido);
    printf("Filme: \033[1m%s\033[0m\n", filmes[pedidoAtual.codigofilme - 1].nome);
    printf("Horário: \033[1m%s\033[0m\n",
         filmes[pedidoAtual.codigofilme - 1].horarios[horarioEscolhido - 1]);
    printf("Data: \033[1m%s\033[0m\n", pedidoAtual.data);
    printf("Ingressos Inteira: \033[1m%d\033[0m\n", pedidoAtual.inteiras);
    printf("Ingressos Meia: \033[1m%d\033[0m\n", pedidoAtual.meias);
    printf("Valor Total: \033[1mR$%d\033[0m\n",
         (pedidoAtual.inteiras * valorIngresso) + (pedidoAtual.meias * (valorIngresso / 2)));
    printf("------------------------\n");
}

//função para salvar pedidos
void salvarPedidoCSV(pedido p, Reserva *lista){
    //abre o arquivo em modo append
    FILE *arquivo = fopen("pedidos.csv", "a");
    if(arquivo == NULL){
        perror("Erro ao abrir o arquivo");
        
        return;
    }
    //retorna os dados do pedido no arquivo
    fprintf(arquivo, "%d,%d,%d,%d,%d,%s,", p.codigopedido, p.inteiras, p.meias, p.dia, p.codigofilme, p.data);

    //escreve as poltronas reservadas
    Reserva *temp = lista;
    while(temp != NULL){
        fprintf(arquivo, "%s%d,", temp->poltrona, temp->numeracao);
        temp = temp->proximo;
    }
    //nova linha para o próximo pedido
    fprintf(arquivo, "\n");
    //fecha o arquivo
    fclose(arquivo);
}

//função para buscar os pedidos
pedido *buscarPedidoCSV(int codigoPedido){
    FILE *arquivo = fopen("pedidos.csv", "r");
    if(arquivo == NULL){
        perror("Erro ao abrir o arquivo");
        return NULL;
    }
    
    char linha[256];
    //o ponteiro do pedido começa como NULL
    pedido *p = NULL;
    
    while(fgets(linha, sizeof(linha), arquivo)){
        //divide a linha em campos usando a vírgula como delimitador
        char *token = strtok(linha, ",");
        int pedidoEncontrado = atoi(token);
    
        if(pedidoEncontrado == codigoPedido){
            // cria uma nova estrutura pedido e preenche com os dados do CSV
            p = (pedido *)malloc(sizeof(pedido));
            p->codigopedido = pedidoEncontrado;
            p->inteiras = atoi(strtok(NULL, ","));
            p->meias = atoi(strtok(NULL, ","));
            p->dia = atoi(strtok(NULL, ","));
            p->codigofilme = atoi(strtok(NULL, ","));
            strcpy(p->data, strtok(NULL, ","));
            //le as poltronas reservadas
            p->numPoltronas = 0;
            p->poltronas = NULL;
            
        char *poltronaToken;
            
        while((poltronaToken = strtok(NULL, ",")) != NULL){
            p->numPoltronas++;
            p->poltronas =
                (char **)realloc(p->poltronas, p->numPoltronas * sizeof(char *));
            //copia a string da poltrona
            p->poltronas[p->numPoltronas - 1] = strdup(poltronaToken);
        }
        //sai do loop após encontrar o pedido
        break;
    }
}
    //fecha o arquivo após o loop de leitura
    fclose(arquivo);
    
    //retorna o pedido encontrado (ou NULL se não encontrado)
    return p;
}

//função para validar as datas
int validarData(const char *date){
    //validar formato da data
    if(strlen(date) != 10 || date[2] != '/' || date[5] != '/'){
        return 0;
    }

    //lendo a data inserida
    int dia, mes, ano;
    sscanf(date, "%2d/%2d/%4d", &dia, &mes, &ano);

    //checar se a data é válida logicamente
    if(dia < 1 || dia > 31 || mes < 1 || mes > 12 || ano < 1900){
        return 0;
    }

    //pega a data atual
    time_t t = time(NULL);
    struct tm *dataAtual = localtime(&t);

    //cria uma estrutura para a data atual
    struct tm digiteData = {0};
    digiteData.tm_mday = dia;
    digiteData.tm_mon = mes - 1;
    digiteData.tm_year = ano - 1900;

    //converte o tempo para fazer a comparação
    time_t digiteTime = mktime(&digiteData);
    double diferencaDias = difftime(digiteTime, t) / (60 * 60 * 24);

    if(diferencaDias < 0 || diferencaDias > 365){
       return 0;
    }
    
    return 1;
}

//função para imprimir datas
void imprimirDatas(pedido *p){
    do{
        printf("Digite a data que deseja assistir ao filme (DD/MM/YYYY): \n");
        scanf("%10s", p->data);

        if(!validarData(p->data)){
            printf("Data inválida ou fora do intervalo permitido. Tente novamente.\n");
        }
    } 
    while(!validarData(p->data));
}

//função para imprimir reservas
void reservarLugares(Reserva *lista){
    Reserva *temp = lista;
    
    while(temp != NULL){
        printf("Assento reservado:");
        printf("Letra: %s. Poltrona: %d.\n", temp->poltrona, temp->numeracao);
        temp = temp->proximo;
    }
}

//função para validar meia-entrada

int validarCarteira() {
    char input[20];
    int carteirinha;

    while (1) {
        printf("Digite o número da carteirinha (8 dígitos):\n");
        scanf("%19s", input); // Limita a entrada para 19 caracteres (8 dígitos + \0)

        // Verifica se a entrada tem 8 caracteres
        if (strlen(input) != 8) {
            printf("ERRO! A carteirinha deve ter exatamente 8 dígitos.\n");
            continue; // Volta para o início do loop
        }

        // verifica se todos os caracteres são dígitos
        int todosDigitos = 1;
        for (int i = 0; i < 8; i++) {
            if (!isdigit(input[i])) {
                todosDigitos = 0;
                break; // sai do loop se algum nao for digito
            }
        }

        if (todosDigitos) {
            // converte a string para inteiro e armazena
            carteirinha = atoi(input);
            printf("\nMeia entrada realizada com sucesso!\n");
            printf("Número da carteirinha: %d\n", carteirinha);
            return 1; // Validação bem-sucedida
        } else {
            printf("ERRO! A carteirinha deve conter apenas números.\n");
        }
    }
}
int main(){

    //mostra o filme, a duração e o horário 
    Filme filmes[MAX_FILMES] = {
    
        {"Barbie", "1h 54m", 
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"Poor Things (Pobres Criaturas)","2h 21min",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"Anatomy of a Fall (Anatomia de uma Queda)","2h 30min",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"Oppenheimer", "3h",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"The Zone of Interest (Zona de Interesse)","1h 45min",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"Past Lives (Vidas Passadas)","1h 46min",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"Segredos de Um Escândalo (May December)","1h 57min",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"Homem-Aranha: Através do Aranhaverso","2h 20min",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"O Menino e a Garça","2h 04min",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}},
    
        {"Dune 2 (Duna 2)","2h 46min",
        {"14:00", "16:00", "18:00", "20:00", "23:00"}}
    
    };

    int escolha;
    
    //todo o código do cineminha está indentando dentro do do while
    do{
        //chamada da função menu principal
        exibirMenuPrincipal();
        scanf("%d", &escolha);

        //se a escolha for = 1(comprar ingressos) todo o código estará identado dentro deste if
        if(escolha == 1){
            srand(time(0));
            pedido pedidoAtual;
            pedidoAtual.codigopedido = rand() % 10000;
            printf("\nNUMERO DO PEDIDO : %d\n", pedidoAtual.codigopedido);
        
            int filmeEscolhido;
            char direitoMeia;
            int acessoMeia = 0;
    
            printf("Você tem direito a meia? (S/N)\n");
            scanf(" %c", &direitoMeia);
           
            //código para o usuário decidir a meia entrada
            while(direitoMeia != 'S' && direitoMeia != 'N'){
                printf("Opção Inválida, responda com S para sim ou N para não.\n");
                scanf("%c", &direitoMeia);
            }
            
            int meia = 0;
            int inteira = 0;
            int valorTotal = 0;

            //se tem direito a meia-entrada
            if(direitoMeia == 'S'){
                //chamada da função que valida a carteirinha
                int validado = validarCarteira();
        
                if(validado){
                  acessoMeia = 1;
                }
            }

            if(acessoMeia){
                printf("Quantidade de ingressos meia-entrada:\n");
                scanf("%d", &meia);
            }
            
            printf("Quantidade de inteiras:\n");
            scanf("%d", &inteira);
        
            while(meia < 0 || inteira < 0){
                printf("Quantidade inválida. Tente novamente.\n");
                
                if(acessoMeia){
                    printf("Quantidade de ingressos meia-entrada:\n");
                    scanf("%d", &meia);
                }
        
                printf("Quantidade de inteiras: ");
                scanf("%d", &inteira);
            }

            valorTotal = (inteira * valorIngresso) + (meia * (valorIngresso / 2));
        
            //código para reservar os assentos
            int numReservas;
            printf("------------------------\n");
            printf("\nRESERVA DE ASSENTOS.\n");
            numReservas = inteira + meia;
        
            Reserva *lista = NULL;
            Reserva *ultima = NULL;
    
            //for para percorrer a quantidade de reservas que o usuário digitar
            for(int i = 0; i < numReservas; i++){
                char poltrona[2];
                int numeracao = i + 1;
    
                printf("Escolha a fila onde você deseja sentar (A, B, C ou D) para a " "poltrona %d:\n", numeracao);
                scanf("%1s", poltrona);
        
                while(poltrona[0] != 'A' && poltrona[0] != 'B' && poltrona[0] != 'C' && poltrona[0] != 'D'){
                    printf("Por favor, digite com letra maiúscula as letras entre A, B, C ou D:\n");
                    scanf("%1s", poltrona);
                }
    
                //reservando memória para reservar as reservas
                Reserva *novaReserva = (Reserva *)malloc(sizeof(Reserva));
                novaReserva->numeracao = numeracao;
                strcpy(novaReserva->poltrona, poltrona);
            
                //nova reserva é definida como vazia
                novaReserva->proximo = NULL;
        
                //se a lista está vazia, ela adiciona a nova reserva digitada
                //anteriormente para a nova lista
                if(lista == NULL){
                    lista = novaReserva;
                }
                //caso contrário, indica que ela já tem reservas salvas, ou ponteiro
                //próximo do outro apontador aponta para uma novaReserva novamente
                else{
                    ultima->proximo = novaReserva;
                }
        
                //a última reserva é adicionada
                ultima = novaReserva;
            }

            //chamada da função imprimir filmes
            exibirFilmesComDetalhes(filmes);
    
            printf("Escolha o filme digitando o número correspondente:\n");
            scanf("%d", &filmeEscolhido);
            printf("Filme escolhido '%s'\n:", filmes[filmeEscolhido - 1].nome);
            pedidoAtual.codigofilme = filmeEscolhido;
        
            //chamada da função imprimir horários
            exibirHorariosComNumeracao(filmes, filmeEscolhido);
                
            int horarioEscolhido;
            printf("Selecione o horário de acordo com a numeração acima:\n");
            scanf("%d", &horarioEscolhido);
        
            pedidoAtual.inteiras = inteira;
            pedidoAtual.meias = meia;
    
            //chamada da função imprimir datas
            imprimirDatas(&pedidoAtual);
            exibirConfirmacaoPedido(pedidoAtual, filmes, horarioEscolhido);
                    
            //chamada da função das reservas
            reservarLugares(lista);
            salvarPedidoCSV(pedidoAtual, lista);
                
            //liberando memória das reservas
            while(lista != NULL){
                //lista fica armazenada pela variável temporária
                Reserva *temp = lista;
                  
                //lista aponta para o próximo e é liberada da variável temporária
                lista = lista->proximo;
                free(temp);
            }
        } 

        //parte do código onde irá checar os pedidos feitos
        else if(escolha == 2){
            int codigoPedido;
            printf("Digite o código do pedido: ");
            scanf("%d", &codigoPedido);
        
            pedido *p = buscarPedidoCSV(codigoPedido);
            if(p != NULL){
                //exibe os detalhes do pedido encontrado
                pedido *p = buscarPedidoCSV(codigoPedido);
                
                if(p != NULL){
                    //exibe todos os detalhes do pedido caso ele seja encontrado
                    printf("Pedido encontrado:\n");
                    printf("Código: %d\n", p->codigopedido);
                    printf("Inteiras: %d\n", p->inteiras);
                    printf("Meias: %d\n", p->meias);
                    printf("Filme: %s\n", filmes[p->codigofilme - 1].nome);
                    printf("Data: %s\n", p->data);
                    printf("Poltronas: ");
                
                    for(int i = 0; i < p->numPoltronas; i++){
                        printf("%s ", p->poltronas[i]);
                        //libera a memória de cada string de poltrona
                        free(p->poltronas[i]);
                    }
                    
                    printf("\n");
                
                    //libera o array de poltronas
                    free(p->poltronas);
                    
                    //libera a memória da estrutura do pedido
                    free(p);
                }                                                                                                                                                                                                                                        
                //caso o pedido não seja encontrado
                else{    
                    printf("Pedido não encontrado.\n");
                    free(p);
                }
            }
        }
        else if(escolha != 0){
            printf("\033[1;31mOpção inválida. Tente novamente.\033[0m\n");
        }
    }
    //while do do while
    while(escolha != 0);
        printf("\033[1;31mSaindo do Cineminha...\033[0m\n");

    return 0;
}
