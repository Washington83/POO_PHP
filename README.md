# Orientação a Objetos

## Abstração

🧠 Conceito básico

Você usa:

classes abstratas (abstract class)
métodos abstratos (abstract function)

Esses métodos não têm implementação na classe pai — quem implementa são as classes filhas.

📌 Exemplo simples
abstract class Animal
{
    abstract public function fazerSom();
}

class Cachorro extends Animal
{
    public function fazerSom()
    {
        echo "Latido";
    }
}

class Gato extends Animal
{
    public function fazerSom()
    {
        echo "Miau";
    }
}

👉 Aqui:

Animal é uma classe abstrata
fazerSom() é um método abstrato (sem corpo)
Cada classe filha é obrigada a implementar
❌ Não pode instanciar classe abstrata
$animal = new Animal(); // ERRO

👉 Classes abstratas servem só como modelo/base

🔧 Abstração com lógica + obrigatoriedade

Uma classe abstrata pode ter:

métodos normais (com código)
métodos abstratos (sem código)
abstract class Veiculo
{
    public function ligar()
    {
        echo "Ligando veículo...";
    }

    abstract public function acelerar();
}

class Carro extends Veiculo
{
    public function acelerar()
    {
        echo "Carro acelerando";
    }
}
🔥 Aplicando no seu exemplo de Carro

Vamos deixar mais profissional usando abstração:

abstract class Veiculo
{
    public string $modelo;
    public int $velocidade = 0;

    public function acelerar(): void
    {
        $this->velocidade++;
    }

    abstract public function tipo(): string;
}

class Carro extends Veiculo
{
    public function tipo(): string
    {
        return "Carro";
    }
}

class Moto extends Veiculo
{
    public function tipo(): string
    {
        return "Moto";
    }
}

✅ Resumindo
abstract class não pode ser instanciada
abstract function deve ser implementada nas filhas
Serve para criar um modelo padrão
Ajuda a organizar e escalar o código

---

## Encapsulamento

Encapsulamento é um dos pilares da Programação Orientada a Objetos. A ideia é **esconder os detalhes internos de uma classe** e expor apenas o que é necessário, controlando o acesso aos dados.

---
### Modificadores de Acesso

Exemplo prático
``` PHP
class ContaBancaria {
    private float $saldo;        // só esta classe acessa
    protected string $titular;   // esta classe e subclasses acessam
    public string $banco;        // qualquer um acessa

    public function __construct(string $titular, float $saldoInicial) {
        $this->titular = $titular;
        $this->saldo = $saldoInicial;
        $this->banco = "Banco XYZ";
    }

    // Getter — leitura controlada
    public function getSaldo(): float {
        return $this->saldo;
    }

    // Setter — escrita com validação
    public function depositar(float $valor): void {
        if ($valor <= 0) {
            throw new InvalidArgumentException("Valor deve ser positivo.");
        }
        $this->saldo += $valor;
    }

    public function sacar(float $valor): void {
        if ($valor > $this->saldo) {
            throw new RuntimeException("Saldo insuficiente.");
        }
        $this->saldo -= $valor;
    }
}

$conta = new ContaBancaria("Maria", 1000.0);

echo $conta->getSaldo();   // ✅ 1000
$conta->depositar(500);    // ✅ saldo vira 1500

// $conta->saldo = 999999; // ❌ Erro! propriedade privada
```

---
Herança e `protected`

``` PHP
class ContaPoupanca extends ContaBancaria {
    public function getTitular(): string {
        return $this->titular; // ✅ acessa protected da classe pai
    }
}
```

---
### Boas práticas

- **Prefira `private`** por padrão e abra acesso só quando necessário.
- Use **getters/setters** para controlar leitura e escrita com validação.
- Nunca exponha propriedades sensíveis como `public` diretamente.
- Use `protected` quando precisar permitir acesso em subclasses.

---

## Herança 

Herança permite que uma classe **herde propriedades e métodos de outra**, evitando repetição de código e criando hierarquias lógicas.

Sintaxe básica com `extends`
``` PHP
class Animal {
    public string $nome;

    public function __construct(string $nome) {
        $this->nome = $nome;
    }

    public function comer(): void {
        echo "{$this->nome} está comendo.";
    }

    public function emitirSom(): void {
        echo "Som genérico...";
    }
}

class Cachorro extends Animal {
    public function emitirSom(): void {        // sobrescreve o método pai
        echo "{$this->nome} faz: Au au!";
    }

    public function buscarBola(): void {       // método próprio
        echo "{$this->nome} buscou a bola!";
    }
}

$dog = new Cachorro("Rex");
$dog->comer();       // ✅ herdado do Animal → "Rex está comendo."
$dog->emitirSom();   // ✅ sobrescrito       → "Rex faz: Au au!"
$dog->buscarBola();  // ✅ próprio do Cachorro
```

---
### `parent::` — acessando o método pai

Quando você sobrescreve um método mas quer **aproveitar o código do pai**:

``` PHP
class Gato extends Animal {
    private int $vidas;

    public function __construct(string $nome, int $vidas = 9) {
        parent::__construct($nome);   // chama o construtor do pai
        $this->vidas = $vidas;
    }

    public function emitirSom(): void {
        parent::emitirSom();          // executa o método do pai primeiro
        echo " — mas o Gato faz: Miau!";
    }
}

$gato = new Gato("Whiskers");
$gato->emitirSom();
// "Som genérico... — mas o Gato faz: Miau!"
```

---
### `abstract` — forçando implementação nas filhas

Classes abstratas **não podem ser instanciadas** e obrigam as subclasses a implementar certos métodos:

``` PHP
abstract class Forma {
    abstract public function area(): float;   // obrigatório nas filhas

    public function descrever(): void {
        echo "Área: " . $this->area();
    }
}

class Circulo extends Forma {
    public function __construct(private float $raio) {}

    public function area(): float {
        return M_PI * $this->raio ** 2;
    }
}

class Retangulo extends Forma {
    public function __construct(
        private float $largura,
        private float $altura
    ) {}

    public function area(): float {
        return $this->largura * $this->altura;
    }
}

$c = new Circulo(5);
$c->descrever();    // "Área: 78.53..."

// $f = new Forma(); // ❌ Erro! Classe abstrata
```

--- 

`final` — impedindo herança ou sobrescrita
``` php
final class Singleton {
    // Ninguém pode herdar desta classe
}

class Animal {
    final public function respirar(): void {
        // Nenhuma subclasse pode sobrescrever este método
        echo "Respirando...";
    }
}
```

---
Resumo visual
``` SHELL
Animal (pai)
├── comer()        ← herdado por todos
└── emitirSom()    ← pode ser sobrescrito
      │
      ├── Cachorro
      │     └── emitirSom() → "Au au!"
      │
      └── Gato
            └── emitirSom() → "Miau!"
```

---
### Regras importantes

- PHP só suporta **herança simples** (uma classe filha, um pai direto).
- Para herdar de múltiplas fontes, use **Interfaces** ou **Traits**.
- Prefira `abstract` quando a classe pai não faz sentido existir sozinha.
- Use `final` para proteger comportamentos críticos de serem alterados.

---

## Polimorfismo

Polimorfismo significa **"muitas formas"** — a capacidade de objetos diferentes responderem ao mesmo método de formas distintas. É o que permite tratar objetos de classes diferentes de forma uniforme.

---
### Tipos de Polimorfismo em PHP

PHP tem dois tipos principais:

---
### 1. Polimorfismo por Herança (sobrescrita)

Já visto com `extends` — métodos com o mesmo nome se comportam diferente em cada subclasse:

``` PHP
abstract class Pagamento {
    abstract public function processar(float $valor): string;

    public function finalizar(float $valor): void {
        echo $this->processar($valor);  // chama a versão correta automaticamente
        echo " — Pagamento concluído!\n";
    }
}

class CartaoCredito extends Pagamento {
    public function processar(float $valor): string {
        return "Cobrando R$ {$valor} no cartão de crédito.";
    }
}

class Pix extends Pagamento {
    public function processar(float $valor): string {
        return "Transferindo R$ {$valor} via Pix.";
    }
}

class Boleto extends Pagamento {
    public function processar(float $valor): string {
        return "Gerando boleto de R$ {$valor}.";
    }
}

// O mesmo código funciona para qualquer tipo de pagamento!
$pagamentos = [
    new CartaoCredito(),
    new Pix(),
    new Boleto(),
];

foreach ($pagamentos as $pagamento) {
    $pagamento->finalizar(150.00);
}

// "Cobrando R$ 150 no cartão de crédito. — Pagamento concluído!"
// "Transferindo R$ 150 via Pix. — Pagamento concluído!"
// "Gerando boleto de R$ 150. — Pagamento concluído!"
```

---
### 2. Polimorfismo por Interface

Interfaces definem um **contrato** que classes não relacionadas podem implementar:

``` PHP
interface Exportavel {
    public function exportar(): string;
}

class Relatorio implements Exportavel {
    public function exportar(): string {
        return "Exportando relatório como PDF...";
    }
}

class Planilha implements Exportavel {
    public function exportar(): string {
        return "Exportando planilha como CSV...";
    }
}

class Imagem implements Exportavel {
    public function exportar(): string {
        return "Exportando imagem como PNG...";
    }
}

// Função aceita qualquer coisa que seja Exportavel
function exportarTudo(Exportavel $item): void {
    echo $item->exportar() . "\n";
}

exportarTudo(new Relatorio());  // "Exportando relatório como PDF..."
exportarTudo(new Planilha());   // "Exportando planilha como CSV..."
exportarTudo(new Imagem());     // "Exportando imagem como PNG..."
```

---
### Type Hinting — a chave do polimorfismo

O segredo está em tipar pelo **tipo pai ou interface**, não pela classe concreta:
``` php
// ❌ Rígido — só aceita Cachorro
function fazerSom(Cachorro $animal): void {
    $animal -> emitirSom();
}

// ✅ Flexível — aceita qualquer Animal
function fazerSom(Animal $animal): void {
    $animal -> emitirSom();
}
```

---

Exemplo completo e real
``` PHP
interface Notificacao {
    public function enviar(string $mensagem): void;
}

class Email implements Notificacao {
    public function enviar(string $mensagem): void {
        echo "📧 Email: {$mensagem}\n";
    }
}

class SMS implements Notificacao {
    public function enviar(string $mensagem): void {
        echo "📱 SMS: {$mensagem}\n";
    }
}

class PushNotification implements Notificacao {
    public function enviar(string $mensagem): void {
        echo "🔔 Push: {$mensagem}\n";
    }
}

class Notificador {
    private array $canais = [];

    public function adicionarCanal(Notificacao $canal): void {
        $this->canais[] = $canal;
    }

    public function notificarTodos(string $mensagem): void {
        foreach ($this->canais as $canal) {
            $canal->enviar($mensagem);   // polimorfismo aqui!
        }
    }
}

$notificador = new Notificador();
$notificador->adicionarCanal(new Email());
$notificador->adicionarCanal(new SMS());
$notificador->adicionarCanal(new PushNotification());

$notificador->notificarTodos("Seu pedido foi aprovado!");
// 📧 Email: Seu pedido foi aprovado!
// 📱 SMS: Seu pedido foi aprovado!
// 🔔 Push: Seu pedido foi aprovado!
```

---
Resumo dos três pilares
``` 
Encapsulamento  →  protege e controla o estado interno
Herança         →  reaproveita código entre classes relacionadas
Polimorfismo    →  trata objetos diferentes de forma uniforme
```

---
### Quando usar cada abordagem

| Situação                                         | Use                   |
| ------------------------------------------------ | --------------------- |
| Classes com relação "é um"                       | Herança + sobrescrita |
| Classes não relacionadas com comportamento comum | Interface             |
| Múltiplos comportamentos em uma classe           | Múltiplas interfaces  |

