#include <string.h>
#include <conio.h>
#include <windows.h>
#include <ctype.h>
#include <stdlib.h>
#include "MyInput.h"

#define TAMANHO_SENHA 9
#define TAMANHO_USUARIO 25
#define TAMANHO_NOME 50
#define TAMANHO_UNIDADE 15

typedef struct {
	int codigo;
	char nome[TAMANHO_NOME];
	char usuario[TAMANHO_USUARIO];
	char senha[TAMANHO_SENHA];
}FORNECEDOR;

typedef struct {
	int codigo;
	char nome[TAMANHO_NOME];
	char usuario[TAMANHO_USUARIO];
	char senha[TAMANHO_SENHA];
}EMPRESA;

typedef struct{
	int codigo;
	char nomeProduto[TAMANHO_NOME];
	int codigoEmpresa;
}PRODUTO;

typedef struct {
	int codigo;
	char nomeMaterial[TAMANHO_NOME];
	char unidade[TAMANHO_UNIDADE];
} MATERIAL;

typedef struct{
	int codProduto;
	int codMaterial;
	int QuantidadeMateriai;
} MATERIALPRODUTO; // Esta struct se refere a uma conexão entre a strct MATERIAL E a struct PRODUTO;
                   // Desta forma apenas um produto pode ter vários materiais necessários para construí-lo;

typedef struct{
	int codProduto;
	int quantidadePedido;
} PEDIDO;

void TelaDeLogin();
void TelaCadastroLogin();
void MenuEmpresa();
void MenuFornecedor();
void CadastroProdutos ();

int main() {
	char opcao;
	
	do {
		system ("cls");	
		printf ("MENU PRINCIPAL:\n\n");
		printf ("DIGITE O NUMERO DA OPCAO DESEJADA. PRESSIONE \"ESC\" PARA SAIR. \n");
		printf ("1. Fazer login.\n");
		printf ("2. Cadastrar novo usuario.\n");

		opcao = getch();
		
		switch (opcao) {
			case '1':	
				printf ("\n");
				TelaDeLogin();
				break;
				
			case '2':
				printf ("\n");
				TelaCadastroLogin();
				break;
								
			default:
				if (opcao != 27) {
					printf ("Opcao invalida.");
					Sleep (700);
				}
				break;
		}
	} while (opcao != 27);
}

void TelaDeLogin() {
	FILE *LoginEmpresa = fopen("LoginEmpresa.dat", "rb");
	FILE *LoginFornecedor = fopen("LoginFornecedor.dat", "rb");
	char usuario[TAMANHO_USUARIO], senha[TAMANHO_SENHA], SenhaCorreta[TAMANHO_SENHA];
	int Usuario_SenhaIncorreta = 1, Tipo= 0;
	EMPRESA empresa;
	FORNECEDOR fornecedor;
	
	printf ("Usuario: ");
	strcpy (usuario, GetString(TAMANHO_USUARIO-1));
	printf ("Senha: ");
	strcpy (senha, GetPassword(TAMANHO_SENHA-1));
	
	while( fread(&empresa, sizeof(EMPRESA), 1, LoginEmpresa)){
			if(strcmp(empresa.usuario, usuario) == 0) {
           		strcpy (SenhaCorreta, empresa.senha);
           		Tipo = 1;
           		Usuario_SenhaIncorreta = 0;
		   }
    }
    while( fread(&fornecedor, sizeof(EMPRESA), 1, LoginFornecedor)){
			if(strcmp(fornecedor.usuario, usuario) == 0) {
           		strcpy (SenhaCorreta, fornecedor.senha);
           		Tipo = 2;
           		Usuario_SenhaIncorreta = 0;
		   }
    }
	
	if (Usuario_SenhaIncorreta == 1) {
		printf ("\n\nUsuario nao cadastrado.\n\n");
		fclose(LoginEmpresa);
	    fclose(LoginFornecedor);
		getch();
	}
	else if (strcmp (senha, SenhaCorreta) == 0) {
		if (Tipo == 1) {
			fclose(LoginEmpresa);
			MenuEmpresa();
		}
		else if (Tipo = 2) {
			fclose(LoginFornecedor);
			MenuFornecedor();
		}
	}
	else {
		printf ("\n\nSenha incorreta.");
	    fclose(LoginEmpresa);
	    fclose(LoginFornecedor);
		getch();
	}
}

void TelaCadastroLogin() {
	FILE *loginEmpresa = fopen("LoginEmpresa.dat", "ab+");
	FILE *loginFornecedor = fopen("LoginFornecedor.dat", "ab+");
	EMPRESA empresa;
	FORNECEDOR fornecedor;
	int JaExiste = 0, Tipo, maior;
	char senha1[TAMANHO_SENHA], senha2[TAMANHO_SENHA], usuario[TAMANHO_USUARIO];
	
	do {
		printf("Digite 1 para empresa ou 2 para fornecedor: ");
		scanf ("%d", &Tipo);	
	} while (Tipo != 1 && Tipo != 2);
	
	if (Tipo == 1) {
		printf ("Nome da empresa: ");
		strcpy (empresa.nome, GetString(TAMANHO_NOME-1));
		
		maior = 1;
		while( fread(&empresa, sizeof(EMPRESA), 1, loginEmpresa)){
			if(empresa.codigo >= maior)
           		maior = empresa.codigo+1;
    	}
    	fseek(loginEmpresa, 0, SEEK_SET);
		
	}
	else {
		printf ("Nome do fornecedor: ");
		strcpy (fornecedor.nome, GetString(TAMANHO_NOME-1));
		
		maior = 1;
		while( fread(&fornecedor, sizeof(FORNECEDOR), 1, loginFornecedor)){
			if(fornecedor.codigo >= maior)
           		maior = fornecedor.codigo+1;
    	}
    	fseek(loginFornecedor, 0, SEEK_SET);
	}
		
	do {
		JaExiste = 0;
		printf ("Usuario: ");
		strcpy (usuario, GetString(TAMANHO_USUARIO-1));
		
		while( fread(&empresa, sizeof(EMPRESA), 1, loginEmpresa)){
			if(strcmp(empresa.usuario, usuario) == 0)
           		JaExiste = 1;
    	}
    	fseek(loginEmpresa, 0, SEEK_SET);
    	
    	while( fread(&fornecedor, sizeof(FORNECEDOR), 1, loginFornecedor)){
			if(strcmp(fornecedor.usuario, usuario) == 0)
           		JaExiste = 1;
    	}
    	fseek(loginFornecedor, 0, SEEK_SET);
    	
    	if (JaExiste == 1)
       		printf ("\nEste usuario ja existe. Digite novamente.\n\n");
		else
			if (Tipo == 1)
				strcpy (empresa.usuario, usuario);
			else
				strcpy (fornecedor.usuario, usuario);
	} while (JaExiste == 1);
	
	do {
		printf ("Senha: ");
		strcpy (senha1, GetPassword(TAMANHO_SENHA-1));
		
		printf ("\nConfirme sua senha: ");
		strcpy (senha2, GetPassword(TAMANHO_SENHA-1));
		
		if (Tipo == 1) {
			empresa.codigo = maior;
			printf ("\nCodigo da empresa: %d", empresa.codigo);
			
			if (strcmp (senha1, senha2) == 0) {
				strcpy (empresa.senha, senha1);
				fwrite(&empresa, sizeof(EMPRESA), 1, loginEmpresa);
			}
			else
				printf ("\n\nSenhas nao correspondem. Digite novamente.\n\n");
		}
		else {
			fornecedor.codigo = maior;
			printf ("\nCodigo do fornecedor: %d", fornecedor.codigo);
			
			if (strcmp (senha1, senha2) == 0) {
				strcpy (fornecedor.senha, senha1);
				fwrite(&fornecedor, sizeof(FORNECEDOR), 1, loginFornecedor);
			}
			else
				printf ("\n\nSenhas nao correspondem. Digite novamente.\n\n");
		}
		
	} while  (strcmp (senha1, senha2) != 0);	
	
    fclose(loginEmpresa);
    fclose(loginFornecedor);
    
    printf("\n\nLogin salvo com sucesso!\n\n");
    system("pause");
}

void MenuEmpresa() {
	char opcao;
	
	do {
		system ("cls");	
		printf ("MENU EMPRESA:\n\n");
		printf ("DIGITE O NUMERO DA OPCAO DESEJADA. PRESSIONE \"ESC\" PARA VOLTAR AO MENU PRINCIPAL. \n");
		printf ("1. Cadastrar Produtos.\n");
		printf ("2. Fazer pedido.\n");
		printf ("3. Listar produtos.\n");
		printf ("4. Listar pedidos.\n");

		opcao = getch();
		
		switch (opcao) {
			case '1':
					
				break;
				
			case '2':
				break;
				
			case '3':
				break;
				
			case '4':
				break;
								
			default:
				if (opcao != 27) {
					printf ("Opcao invalida.");
					Sleep (700);
				}
				break;
		}
	} while (opcao != 27);
}

void MenuFornecedor() {
	char opcao;
	
	do {
		system ("cls");	
		printf ("MENU FORNECEDOR:\n\n");
		printf ("DIGITE O NUMERO DA OPCAO DESEJADA. PRESSIONE \"ESC\" PARA VOLTAR AO MENU PRINCIPAL. \n");
		printf ("1. Pedidos.\n");
		printf ("2. Cadastrar materiais disponiveis.\n");

		opcao = getch();
		
		switch (opcao) {
			case '1':	
				break;
				
			case '2':	
				break;
								
			default:
				if (opcao != 27) {
					printf ("Opcao invalida.");
					Sleep (700);
				}
				break;
		}
	} while (opcao != 27);
}

void CadastroProdutos () {
	FILE *Produto = fopen ("produtos.dat", "ab+");
	PRODUTO DadosProduto;
	
	printf ("CADASTRO DE PRODUTOS: \n");
	printf ("Nome do produto: ");
	
}
