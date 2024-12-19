# ATT Sistema bancario simples

def linha(tam=32):
    print('-' * tam)


def mensagem_erro(msg):
    print(f'\033[31m{msg}\033[m')


def interface(nomes):
    """Exibe o menu de opções e retorna a escolha do usuário."""
    cont = 1
    print('-' * 35)
    for c in nomes:
        print(f'{cont} - {c}')
        cont += 1
    print('-' * 35)
    return leia_int('Escolha sua opção: ')


def mostra(msg):
    """Exibe uma mensagem formatada."""
    print('-' * 35)
    print(msg.center(35))


def leia_float(msg):
    """Lê um número decimal, tratando erros de entrada."""
    while True:
        try:
            num = float(input(msg).replace(',','.'))
        except ValueError:
            mensagem_erro('Digite apenas números')
        else:
            return num


def leia_int(msg):
    """Lê um número inteiro, tratando erros de entrada."""
    while True:
        try:
            num = int(input(msg))
        except ValueError:
            mensagem_erro('Digite apenas números inteiros')
        else:
            return num
    

def validacao_acesso(msg):
    """Valida resposta de acesso (S/N)."""
    while True:
        l = str(input(msg)).upper().strip()[0]
        if l in ('S', 'N'):
            return l
        mensagem_erro('Entrada inválida, digite apenas S ou N')
    

def deposito(valor_depositado,msg):
    """Realiza depósito e exibe saldo atualizado."""
    valor_inical = valor_depositado
    while True:
        try:
            num = leia_float(msg)
        except ValueError:
           mensagem_erro('Digite apenas números')
        else:
            valor_depositado += num
            historico_deposito.append(num)
            mostra(f'\033[33mSeu saldo anterior é R${valor_inical:.2f}\033[m\n\033[32mFoi depositado R${num:.2f}\033[m\nSaldo atual R${valor_depositado:.2f}')
        return valor_depositado


def saque(valor_depositado,limite_diario_saque,msg):
    """Realiza saque, verifica saldo e limite diário."""
    valor_antes = valor_depositado
    if  limite_diario_saque == 0:
        mensagem_erro('Seu limite de saques diario acabou')
        return valor_depositado, limite_diario_saque
    
    elif valor_depositado == 0:
        mensagem_erro('Você não possui dinheiro para saque')
        return valor_depositado, limite_diario_saque
    s = leia_float(msg)

    if s <= 0:
        print('\033[31mDigite apenas números maior que R$0\033[m')
        return valor_depositado, limite_diario_saque
    
    if valor_depositado > s: 
        valor_depositado -= s
        historico_saque.append(s)
        limite_diario_saque -= 1
        mostra(f'\033[33mSeu saldo era R${valor_antes:.2f}\033[m\n\033[31mSaque de R${s:.2f}\033[m\nSaldo atual R${valor_depositado}\nRestam {limite_diario_saque} saques diários')
        return valor_depositado, limite_diario_saque
    
    else:
        print(f'Saldo insuficiente para saque:\nValor na conta {valor_depositado}')
       
    return valor_depositado, limite_diario_saque
    

def cadastro_nome(msg):
    nome = str(input(msg).strip())
    usuario['Nome'] = nome
    

def cadastro_idade(msg):
    idade = int(input(msg))
    if idade < 0:
        mensagem_erro('Digite uma idade válida')
    usuario['Idade'] = idade
    return idade


def maior_idade():
    if usuario['Idade'] < 18:
        mensagem_erro('Você é menor de idade, impossivel realizar o acesso')
        exit()


def cadastro_cpf(msg):
    cpf = leia_int(msg)
    if cpf < 0:
        mensagem_erro('Digite um CPF válido')
    if cpf in usuario_cpf:
        mensagem_erro('Usuario já cadastrado')
        return 0
    else:
        usuario_cpf.append(cpf)
        return cpf


def nova_conta(msg):
    cpf = int(input(msg))
    if cpf in usuario_cpf:
        linha()
        print('Conta criada com sucesso')
        nova_conta_cpf.append(cpf)
    else:
        mensagem_erro('CPF não encontrado')


def novo_usuario():
    mostra('Cadastro como novo usuario:')
    print()
    cpf = cadastro_cpf('Somente os números do CPF: ')
    if cpf:
        cadastro_nome('Nome: ')
        cadastro_idade('Idade: ')
        maior_idade()
        mostra('\033[32mUsuario criado com sucesso\033[m')
   

def listar_contas():
    """Lista contas cadastradas."""
    if nova_conta_cpf == []:
        linha()
        print('Crie uma nova conta.')
    else:
        cont = len(nova_conta_cpf)
        cont_conta = 0
        while cont > 0:
            linha()
            print('Agência:\t0001')
            print(f'Conta:\t\t{cont_conta + 1}')
            print(f'Titular:\t{usuario["Nome"]}')
            cont -=1
            cont_conta +=1


def extrato():
    """Exibe extrato bancário."""
    mostra('Extrato:\n')
    for saq in historico_saque:
        print(f'\033[31mSaque de:\t\tR${saq}\033[m')
    print('\n')
    for dep in historico_deposito:
        print(f'\033[32mDeposito de:\t\tR${dep}\033[m')
    print('\n')
    print(f'\033[34mSaldo atual:\t\tR${valor_depositado}\n\033[m')
    print(f'Restam {limite_diario_saque} saques diários')
    

n = 0
conta = str('0001')
agencia = int(1)
limite_diario_saque = 3
valor_depositado = 0 
usuario = {}
usuario_cpf = []
nova_conta_cpf = []
historico_saque = []
historico_deposito = []

v = validacao_acesso('É seu primeiro acesso? [S/N] ')
if v == 'S':
    mostra('Cadastro como novo usuario:')
    print()
    cpf = cadastro_cpf('Somente os números do CPF: ')
    if cpf:
        cadastro_nome('Nome: ')
        cadastro_idade('idade: ')
        maior_idade()
        mostra('\033[32mUsuario criado com sucesso\033[m')
    
while True:
    mostra('MENU')
    n = interface(['Deposito', 'Saque', 'Extrato', 'Nova Conta', 'Novo Usuário','Listar Contas', 'Sair'])
    if n == 1:
        valor_depositado = deposito(valor_depositado,'Deseja depositar qual valor? R$')
    elif n == 2:
        valor_depositado, limite_diario_saque = saque(valor_depositado,limite_diario_saque,'Deseja sacar qual valor? R$')
    elif n == 3:
        extrato()
    elif n == 4:
        nova_conta('Informe o CPF: ')
    elif n ==5:
        novo_usuario()
    elif n == 6:
        listar_contas()
    elif n == 7:
        print('Volte sempre')
        break
    
