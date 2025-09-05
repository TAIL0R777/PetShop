from typing import List, Dict, Optional

# ==============================
# Classe Cliente
# ==============================
class Cliente:
    def __init__(self, nome: str, endereco: str):
        self.nome = nome
        self.endereco = endereco
        self.pets: List['Pet'] = []

    def adicionar_pet(self, pet: 'Pet'):
        self.pets.append(pet)

    def remover_pet(self, nome_pet: str):
        self.pets = [pet for pet in self.pets if pet.nome != nome_pet]

    def __str__(self):
        return f"Cliente: {self.nome}, Endereço: {self.endereco}, Pets: {[pet.nome for pet in self.pets]}"


# ==============================
# Classe Pet (genérica)
# ==============================
class Pet:
    def __init__(self, nome: str, raca: str, idade: int, tipo: str):
        self.nome = nome
        self.raca = raca
        self.idade = idade
        self.tipo = tipo

    def __str__(self):
        return f"{self.tipo} - Nome: {self.nome}, Raça: {self.raca}, Idade: {self.idade}"


class Mamifero(Pet):
    def __init__(self, nome: str, raca: str, idade: int, pelagem: str):
        super().__init__(nome, raca, idade, "Mamífero")
        self.pelagem = pelagem


class Peixe(Pet):
    def __init__(self, nome: str, raca: str, idade: int, agua_doce: bool):
        super().__init__(nome, raca, idade, "Peixe")
        self.agua_doce = agua_doce


class Ave(Pet):
    def __init__(self, nome: str, raca: str, idade: int, sabe_voar: bool):
        super().__init__(nome, raca, idade, "Ave")
        self.sabe_voar = sabe_voar


# ==============================
# Classe Funcionário (genérica)
# ==============================
class Funcionario:
    def __init__(self, nome: str, endereco: str, salario: float):
        self.nome = nome
        self.endereco = endereco
        self.salario = salario

    def __str__(self):
        return f"Funcionário: {self.nome}, Salário: R${self.salario:.2f}"


class Gestor(Funcionario):
    def __init__(self, nome: str, endereco: str, salario: float, setor: str):
        super().__init__(nome, endereco, salario)
        self.setor = setor


class Veterinario(Funcionario):
    def __init__(self, nome: str, endereco: str, salario: float, especialidade: str):
        super().__init__(nome, endereco, salario)
        self.especialidade = especialidade


# ==============================
# Classe Produto
# ==============================
class Produto:
    def __init__(self, nome: str, descricao: str, valor: float):
        self.nome = nome
        self.descricao = descricao
        self.valor = valor

    def __str__(self):
        return f"Produto: {self.nome}, Valor: R${self.valor:.2f}"


# ==============================
# Classe Estoque
# ==============================
class Estoque:
    def __init__(self):
        self.produtos: Dict[str, Dict[str, any]] = {}

    def adicionar_produto(self, produto: Produto, quantidade: int):
        if produto.nome in self.produtos:
            self.produtos[produto.nome]["quantidade"] += quantidade
        else:
            self.produtos[produto.nome] = {"produto": produto, "quantidade": quantidade}

    def remover_produto(self, nome_produto: str):
        if nome_produto in self.produtos:
            del self.produtos[nome_produto]

    def buscar_produto(self, nome_produto: str) -> Optional[Produto]:
        return self.produtos.get(nome_produto, {}).get("produto")

    def listar_produtos(self):
        for item in self.produtos.values():
            produto = item["produto"]
            quantidade = item["quantidade"]
            print(f"{produto.nome} - {quantidade} unidades - R${produto.valor:.2f}")


# ==============================
# Classe Sistema PetShop
# ==============================
class SistemaPetShop:
    def __init__(self):
        self.clientes: List[Cliente] = []
        self.funcionarios: List[Funcionario] = []
        self.estoque = Estoque()

    # ---------- CADASTRO ----------
    def cadastrar_cliente(self, cliente: Cliente):
        self.clientes.append(cliente)

    def cadastrar_funcionario(self, funcionario: Funcionario):
        self.funcionarios.append(funcionario)

    def cadastrar_produto(self, produto: Produto, quantidade: int):
        self.estoque.adicionar_produto(produto, quantidade)

    # ---------- BUSCA ----------
    def buscar_cliente(self, nome: str) -> Optional[Cliente]:
        for cliente in self.clientes:
            if cliente.nome == nome:
                return cliente
        return None

    def buscar_pet_por_raca(self, raca: str) -> List[Pet]:
        resultado = []
        for cliente in self.clientes:
            for pet in cliente.pets:
                if pet.raca == raca:
                    resultado.append(pet)
        return resultado

    def buscar_pet_por_tipo(self, tipo: str) -> List[Pet]:
        resultado = []
        for cliente in self.clientes:
            for pet in cliente.pets:
                if pet.tipo.lower() == tipo.lower():
                    resultado.append(pet)
        return resultado

    def buscar_funcionario(self, nome: str) -> Optional[Funcionario]:
        for func in self.funcionarios:
            if func.nome == nome:
                return func
        return None

    # ---------- EXCLUSÃO ----------
    def remover_cliente(self, nome: str):
        self.clientes = [c for c in self.clientes if c.nome != nome]

    def remover_funcionario(self, nome: str):
        self.funcionarios = [f for f in self.funcionarios if f.nome != nome]


# ==============================
# Menu Interativo
# ==============================
def menu():
    sistema = SistemaPetShop()

    while True:
        print("\n===== SISTEMA PETSHOP =====")
        print("1 - Cadastrar Cliente")
        print("2 - Cadastrar Funcionário")
        print("3 - Cadastrar Produto")
        print("4 - Listar Clientes")
        print("5 - Listar Funcionários")
        print("6 - Listar Produtos")
        print("7 - Buscar Pets por Raça")
        print("8 - Buscar Pets por Tipo")
        print("9 - Remover Cliente")
        print("10 - Remover Funcionário")
        print("11 - Remover Pet de Cliente")
        print("0 - Sair")

        opcao = input("Escolha uma opção: ")

        if opcao == "1":
            nome = input("Nome do cliente: ")
            endereco = input("Endereço: ")
            cliente = Cliente(nome, endereco)

            qtd_pets = int(input("Quantos pets cadastrar para este cliente? "))
            for _ in range(qtd_pets):
                tipo = input("Tipo do pet (Mamífero/Peixe/Ave): ").capitalize()
                nome_pet = input("Nome do pet: ")
                raca = input("Raça: ")
                idade = int(input("Idade: "))

                if tipo == "Mamífero":
                    pelagem = input("Pelagem: ")
                    pet = Mamifero(nome_pet, raca, idade, pelagem)
                elif tipo == "Peixe":
                    agua_doce = input("Água doce? (s/n): ").lower() == "s"
                    pet = Peixe(nome_pet, raca, idade, agua_doce)
                elif tipo == "Ave":
                    sabe_voar = input("Sabe voar? (s/n): ").lower() == "s"
                    pet = Ave(nome_pet, raca, idade, sabe_voar)
                else:
                    print("Tipo inválido! Pet não cadastrado.")
                    continue

                cliente.adicionar_pet(pet)

            sistema.cadastrar_cliente(cliente)
            print("Cliente cadastrado com sucesso!")

        elif opcao == "2":
            nome = input("Nome do funcionário: ")
            endereco = input("Endereço: ")
            salario = float(input("Salário: "))
            cargo = input("Cargo (Gestor/Veterinário): ").capitalize()

            if cargo == "Gestor":
                setor = input("Setor: ")
                funcionario = Gestor(nome, endereco, salario, setor)
            elif cargo == "Veterinário":
                especialidade = input("Especialidade: ")
                funcionario = Veterinario(nome, endereco, salario, especialidade)
            else:
                print("Cargo inválido!")
                continue

            sistema.cadastrar_funcionario(funcionario)
            print("Funcionário cadastrado com sucesso!")

        elif opcao == "3":
            nome = input("Nome do produto: ")
            descricao = input("Descrição: ")
            valor = float(input("Valor: "))
            quantidade = int(input("Quantidade: "))
            produto = Produto(nome, descricao, valor)
            sistema.cadastrar_produto(produto, quantidade)
            print("Produto cadastrado com sucesso!")

        elif opcao == "4":
            print("\n--- Clientes ---")
            for cliente in sistema.clientes:
                print(cliente)

        elif opcao == "5":
            print("\n--- Funcionários ---")
            for funcionario in sistema.funcionarios:
                print(funcionario)

        elif opcao == "6":
            print("\n--- Produtos ---")
            sistema.estoque.listar_produtos()

        elif opcao == "7":
            raca = input("Digite a raça: ")
            pets = sistema.buscar_pet_por_raca(raca)
            if pets:
                for pet in pets:
                    print(pet)
            else:
                print("Nenhum pet encontrado com essa raça.")

        elif opcao == "8":
            tipo = input("Digite o tipo (Mamífero/Peixe/Ave): ")
            pets = sistema.buscar_pet_por_tipo(tipo)
            if pets:
                for pet in pets:
                    print(pet)
            else:
                print("Nenhum pet encontrado desse tipo.")

        elif opcao == "9":
            nome = input("Nome do cliente a remover: ")
            sistema.remover_cliente(nome)
            print("Cliente removido.")

        elif opcao == "10":
            nome = input("Nome do funcionário a remover: ")
            sistema.remover_funcionario(nome)
            print("Funcionário removido.")

        elif opcao == "11":
            nome_cliente = input("Nome do cliente: ")
            cliente = sistema.buscar_cliente(nome_cliente)
            if cliente:
                print(f"Pets do cliente {cliente.nome}: {[pet.nome for pet in cliente.pets]}")
                nome_pet = input("Nome do pet a remover: ")
                cliente.remover_pet(nome_pet)
                print(f"Pet '{nome_pet}' removido de {cliente.nome}.")
            else:
                print("Cliente não encontrado.")

        elif opcao == "0":
            print("Saindo do sistema...")
            break

        else:
            print("Opção inválida! Tente novamente.")


# ==============================
# Executar o menu
# ==============================
if __name__ == "__main__":
    menu()
