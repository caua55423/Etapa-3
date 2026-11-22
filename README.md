/******************************************************************************

NOME:CAUÃ HENRIQUE RIBEIRO
MATRICULA:29076
DATA DE INICIO:18/11/2026
DATA DE TERMINO:21/11/2026
TRABALHO:INF101

*******************************************************************************/
#include <iostream>
#include <fstream>
#include <string>

using namespace std;

// Dados das contas
int numeroConta[100];
string nomeCliente[100];
string cpf[100];
int tipoConta[100];
double saldo[100];
int contaAtiva[100];
int quantidadeContas = 0;

// Historico de movimentacoes
int contaMov[500];
char tipoMov[500];
double valorMov[500];
int quantidadeMov = 0;

// Funcoes do programa
void carregarContas();
void salvarContas();
void carregarMovimentacoes();
void salvarMovimentacoes();
int procurarConta(int numero);
void cadastrarConta();
void listarContas();
void consultarConta();
void depositar();
void sacar();
void transferir();
void extrato();

int main() {
    int opcao;

    // Quando o programa abre carrega os dados que ja foram salvos
    carregarContas();
    carregarMovimentacoes();

    do {
        cout << "\n===== SISTEMA BANCARIO =====\n";
        cout << "1 - Cadastrar conta\n";
        cout << "2 - Listar contas\n";
        cout << "3 - Consultar conta\n";
        cout << "4 - Depositar\n";
        cout << "5 - Sacar\n";
        cout << "6 - Transferir\n";
        cout << "7 - Ver extrato\n";
        cout << "0 - Sair\n";
        cout << "Escolha uma opcao: ";
        cin >> opcao;

        switch (opcao) {
            case 1:
                cadastrarConta();
                break;
            case 2:
                listarContas();
                break;
            case 3:
                consultarConta();
                break;
            case 4:
                depositar();
                break;
            case 5:
                sacar();
                break;
            case 6:
                transferir();
                break;
            case 7:
                extrato();
                break;
            case 0:
                cout << "Programa encerrado!\n";
                break;
            default:
                cout << "Opcao invalida!\n";
        }

    } while (opcao != 0);

    return 0;
}

// Carrega as contas do arquivo
void carregarContas() {
    ifstream arquivo("contas.txt");
    string linha;

    while (getline(arquivo, linha) && quantidadeContas < 100) {
        int p1 = linha.find(';');
        int p2 = linha.find(';', p1 + 1);
        int p3 = linha.find(';', p2 + 1);
        int p4 = linha.find(';', p3 + 1);
        int p5 = linha.find(';', p4 + 1);

        if (p1 == -1 || p2 == -1 || p3 == -1 || p4 == -1 || p5 == -1)
            continue;

        numeroConta[quantidadeContas] = stoi(linha.substr(0, p1));
        nomeCliente[quantidadeContas] = linha.substr(p1 + 1, p2 - p1 - 1);
        cpf[quantidadeContas] = linha.substr(p2 + 1, p3 - p2 - 1);
        tipoConta[quantidadeContas] = stoi(linha.substr(p3 + 1, p4 - p3 - 1));
        saldo[quantidadeContas] = stod(linha.substr(p4 + 1, p5 - p4 - 1));
        contaAtiva[quantidadeContas] = stoi(linha.substr(p5 + 1));

        quantidadeContas++;
    }

    arquivo.close();
}

// Salva todas as contas no arquivo
void salvarContas() {
    ofstream arquivo("contas.txt");

    for (int i = 0; i < quantidadeContas; i++) {
        arquivo << numeroConta[i] << ";"
                << nomeCliente[i] << ";"
                << cpf[i] << ";"
                << tipoConta[i] << ";"
                << saldo[i] << ";"
                << contaAtiva[i] << endl;
    }

    arquivo.close();
}

// Carrega o historico de movimentacoes
void carregarMovimentacoes() {
    ifstream arquivo("movimentacoes.txt");
    string linha;

    while (getline(arquivo, linha) && quantidadeMov < 500) {
        int p1 = linha.find(';');
        int p2 = linha.find(';', p1 + 1);

        if (p1 == -1 || p2 == -1)
            continue;

        contaMov[quantidadeMov] = stoi(linha.substr(0, p1));
        tipoMov[quantidadeMov] = linha[p1 + 1];
        valorMov[quantidadeMov] = stod(linha.substr(p2 + 1));

        quantidadeMov++;
    }

    arquivo.close();
}

// Salva o historico no arquivo
void salvarMovimentacoes() {
    ofstream arquivo("movimentacoes.txt");

    for (int i = 0; i < quantidadeMov; i++) {
        arquivo << contaMov[i] << ";"
                << tipoMov[i] << ";"
                << valorMov[i] << endl;
    }

    arquivo.close();
}

// Procura uma conta pelo numero
int procurarConta(int numero) {
    for (int i = 0; i < quantidadeContas; i++) {
        if (numeroConta[i] == numero) {
            return i;
        }
    }

    return -1;
}

// Cadastra uma conta nova
void cadastrarConta() {
    if (quantidadeContas == 100) {
        cout << "Limite de contas atingido!\n";
        return;
    }

    int numero;
    cout << "Digite o numero da conta: ";
    cin >> numero;

    if (procurarConta(numero) != -1) {
        cout << "Esse numero de conta ja existe!\n";
        return;
    }

    numeroConta[quantidadeContas] = numero;

    cout << "Nome do cliente: ";
    cin.ignore();
    getline(cin, nomeCliente[quantidadeContas]);

    cout << "CPF: ";
    getline(cin, cpf[quantidadeContas]);

    cout << "Tipo da conta (1 - Corrente / 2 - Poupanca): ";
    cin >> tipoConta[quantidadeContas];

    if (tipoConta[quantidadeContas] != 1 && tipoConta[quantidadeContas] != 2) {
        cout << "Tipo de conta invalido!\n";
        return;
    }

    saldo[quantidadeContas] = 0;
    contaAtiva[quantidadeContas] = 1;

    quantidadeContas++;
    salvarContas();

    cout << "Conta cadastrada com sucesso!\n";
}

// Mostra todas as contas
void listarContas() {
    if (quantidadeContas == 0) {
        cout << "Ainda nao existem contas cadastradas.\n";
        return;
    }

    for (int i = 0; i < quantidadeContas; i++) {
        cout << "\nNumero: " << numeroConta[i] << endl;
        cout << "Cliente: " << nomeCliente[i] << endl;
        cout << "CPF: " << cpf[i] << endl;
        cout << "Tipo: " << (tipoConta[i] == 1 ? "Corrente" : "Poupanca") << endl;
        cout << "Saldo: R$ " << saldo[i] << endl;
        cout << "Situacao: " << (contaAtiva[i] == 1 ? "Ativa" : "Inativa") << endl;
    }
}

// Consulta uma conta
void consultarConta() {
    int numero;
    cout << "Digite o numero da conta: ";
    cin >> numero;

    int posicao = procurarConta(numero);

    if (posicao == -1) {
        cout << "Conta nao encontrada!\n";
    } else {
        cout << "Cliente: " << nomeCliente[posicao] << endl;
        cout << "CPF: " << cpf[posicao] << endl;
        cout << "Saldo: R$ " << saldo[posicao] << endl;
        cout << "Situacao: " << (contaAtiva[posicao] == 1 ? "Ativa" : "Inativa") << endl;
    }
}

// Faz um deposito
void depositar() {
    int numero;
    double valor;

    cout << "Numero da conta: ";
    cin >> numero;

    int posicao = procurarConta(numero);

    if (posicao == -1 || contaAtiva[posicao] == 0) {
        cout << "Conta nao encontrada ou inativa!\n";
        return;
    }

    cout << "Valor do deposito: ";
    cin >> valor;

    if (valor <= 0 || quantidadeMov == 500) {
        cout << "Valor invalido ou limite de movimentacoes atingido!\n";
        return;
    }

    saldo[posicao] += valor;
    contaMov[quantidadeMov] = numero;
    tipoMov[quantidadeMov] = 'D';
    valorMov[quantidadeMov] = valor;
    quantidadeMov++;

    salvarContas();
    salvarMovimentacoes();

    cout << "Deposito realizado!\n";
}

// Faz um saque
void sacar() {
    int numero;
    double valor;

    cout << "Numero da conta: ";
    cin >> numero;

    int posicao = procurarConta(numero);

    if (posicao == -1 || contaAtiva[posicao] == 0) {
        cout << "Conta nao encontrada ou inativa!\n";
        return;
    }

    cout << "Valor do saque: ";
    cin >> valor;

    if (valor <= 0) {
        cout << "Digite um valor maior que zero!\n";
    } else if (saldo[posicao] < valor) {
        cout << "Saldo insuficiente!\n";
    } else if (quantidadeMov == 500) {
        cout << "Limite de movimentacoes atingido!\n";
    } else {
        saldo[posicao] -= valor;
        contaMov[quantidadeMov] = numero;
        tipoMov[quantidadeMov] = 'S';
        valorMov[quantidadeMov] = valor;
        quantidadeMov++;

        salvarContas();
        salvarMovimentacoes();

        cout << "Saque realizado!\n";
    }
}

//Faz uma transferencia entre duas contas
void transferir() {
    int contaOrigem, contaDestino;
    double valor;

    cout << "Conta de origem: ";
    cin >> contaOrigem;

    cout << "Conta de destino: ";
    cin >> contaDestino;

    int origem = procurarConta(contaOrigem);
    int destino = procurarConta(contaDestino);

    if (origem == -1 || destino == -1) {
        cout << "Uma das contas nao foi encontrada!\n";
        return;
    }

    if (origem == destino || contaAtiva[origem] == 0 || contaAtiva[destino] == 0) {
        cout << "Confira as contas informadas!\n";
        return;
    }

    cout << "Valor da transferencia: ";
    cin >> valor;

    if (valor <= 0 || saldo[origem] < valor) {
        cout << "Valor invalido ou saldo insuficiente!\n";
        return;
    }

    if (quantidadeMov > 497) {
        cout << "Limite de movimentacoes atingido!\n";
        return;
    }

    saldo[origem] -= valor;
    saldo[destino] += valor;

    // A transferencia aparece como saida na origem e entrada no destino
    contaMov[quantidadeMov] = contaOrigem;
    tipoMov[quantidadeMov] = 'S';
    valorMov[quantidadeMov] = valor;
    quantidadeMov++;

    contaMov[quantidadeMov] = contaDestino;
    tipoMov[quantidadeMov] = 'D';
    valorMov[quantidadeMov] = valor;
    quantidadeMov++;

    salvarContas();
    salvarMovimentacoes();

    cout << "Transferencia realizada!\n";
}

//Mostra o historico de uma conta
void extrato() {
    int numero;
    cout << "Numero da conta: ";
    cin >> numero;

    int posicao = procurarConta(numero);

    if (posicao == -1) {
        cout << "Conta nao encontrada!\n";
        return;
    }

    cout << "\nExtrato de " << nomeCliente[posicao] << endl;
    cout << "-----------------------------\n";

    bool encontrou = false;

    for (int i = 0; i < quantidadeMov; i++) {
        if (contaMov[i] == numero) {
            encontrou = true;

            if (tipoMov[i] == 'D') {
                cout << "Deposito: R$ " << valorMov[i] << endl;
            } else {
                cout << "Saque: R$ " << valorMov[i] << endl;
            }
        }
    }

    if (!encontrou) {
        cout << "Essa conta ainda nao tem movimentacoes.\n";
    }

    cout << "Saldo atual: R$ " << saldo[posicao] << endl;
}
