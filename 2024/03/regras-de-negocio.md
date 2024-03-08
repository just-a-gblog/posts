# Do Conceito à Codificação: Transformando Regras de Negócio em Códigos Confiáveis


**YAN JUSTINO**
[MSc. Software Engineering](https://repositorio.ufrn.br/handle/123456789/26370) · [PhD. Student](https://www.cesar.school/doutorado-profissional-em-engenharia-de-software/)
[AWS](https://www.youracclaim.com/users/yan-justino/badges) · [MCSD](https://www.youracclaim.com/users/yan-justino/badges) · [OCA](https://www.youracclaim.com/users/yan-justino/badges) · [ORCID](https://orcid.org/0000-0001-7248-716X)  · [Tech Lead at ITAÚ Unibanco]()

![Static Badge](https://img.shields.io/badge/post-design-50A6C5?style=for-the-badge)

----

> As fronteiras da minha linguagem são as fronteiras do meu universo - **Ludwig Wittgenstein**.

----


## CONTEXTO

A engenharia de software pode empregar muito esforço para alcançar uma clara definição de fronteiras de um sistema. Parte desse trabalho está na concepção de módulos, componentes e seus conectores, bem como as estruturas de alocação que satisfazem um conjunto de requisitos que, mediante determinadas expectativas de qualidade, respondem às demandas do negócio.

Em colaboração com especialitas de negócio, o papel do desenvolvimento é "preencher" as lacunas dessas estruturas de software com comportamentos (ricos) do domínio. Para isso, equipes de desenvolvimento adotam métodos como _Business Process Management_ (BPM), _Domain-Driven Design_ (DDD), _Business Capability Analysis_ etc, para representar contextos do domínio e expressar regras negócio.

Regras de negócio são as diretrizes, critérios, ou condições definidas dentro de uma organização para governar seus processos, operações e transações. Elas abrangem uma ampla gama de áreas podem especificar: critérios de elegibilidade, políticas, processos, padrões etc. Frequentemente regas de negócio são implementadas em sistemas de informação. Esse tipo de automatização permite um monitoramente eficiente dessas regras no cotidiando operacional das organizações.

No entanto, a automatização de regras de negócio em sistemas de informação também demanda um nível de qualidade que garanta confiabilidade nas operações. Nesse sentido, é importante que desenvolvedores implementem os comportamentos de negócio de forma adequada ao ponto de serem livres de efeitos colaterais e fáceis de manter. Caso contrário, o sistema apresentará falhas que podem representar riscos para organização e principalmente para as pessoas.

Comparando as exigências de qualidade sobre sistemas de informação com as práticas de desenvolvimento de software devemos nos questionar:
 
- _Como desenvolvedores implementam regras de negócio nos sistemas de informação?_
- _Quais práticas podem ser adotadas para codificar regras de forma a garantir a sua corretude?_
- _Quais as estratégias a equipe de desenvolvimento adota para gerenciar o ciclo de vida das regras no sistema?_

Essas questões serão o guia deste post, que tem por objetivo apresentar conceitos e práticas de design desoftware que podem gerar insgihts valiosos para auxiliar desenvolvedores na modelagem, construção e manutenção de regras de negócio em sistemas de informação.

## BACKGROUND

### Regras de negócio em Domain-Driven Design

No intuito de aproximar a linguagem de negócio ao design de software, [Eric Evans](https://en.wikipedia.org/wiki/Domain-driven_design), em _Domain-Driven Design_, propõe a criação de modelos de domínio ricos que representam um "conhecimento destilado" do negócio. Nesse sentido, Evans estabelece os modelos como "a espinha dorsal de uma linguagem utilizada por todos os membros da equipe".

No entanto, a compreensão do que venha ser um modelo rico é muito abstrata e pode ser repleta de equívocos. Um deles é pensar que um modelo rico se resume a uma espécie de contraste à modelos anêmicos (classes desprovidas de comportamento). Uma das consequências dessa visão restrita, é a construção de entidades excessivamente ricas em comportamentos. Segundo os autores do livro _Patterns, Principles, and Practices of Domain-Driven Design_:

---
<div>
<center>
<strong>✍🏼 CITAÇÃO</strong>
<br/>
<small>
Pessoas novas no DDD tendem a modelar muitos comportamentos. Eles inocentemente acreditam na falácia de que o DDD trata 
de modelar o mundo real. Posteriormente, eles tentam modelar muitos comportamentos do mundo real de uma entidade.
<br/>
<strong>Scott Millett e Nick Tune</strong>
</small>
</center>
</div>
---    


Evans ainda afirma que "as regras de um negócio geralmente não se enquadram na responsabilidade de uma Entidade ou Objeto de valor", uma vez que elas "podem sobrecarregar o significado básico do objeto do domínio". No entanto, ele reitera que 'retirar as regras da camada de domínio é ainda pior'". Para resolver esse impasse, Evans, toma "emprestado o conceito de predicados e cria objetos especializados que avaliam e fornecem como resposta um resultado booleano".

---
<div>
<center>
<strong>💡SAIBA MAIS</strong>
<br/>
<small>
Os conceitos de predicado e resultados booleanos remontam um movimento filosófico-matemático do início do século XX chamado de Virada Linguística. Nesse período, os filósofos adotaram a linguagem como foco central de suas investigações. Pensadores como Frege, Russel e Wittgentein, fazendo uso da lógica e da matemática, procuraram uma abordagem formal para explicar e abstrair os fenômenos linguísticos. Esses estudos têm grande influência sobre como hoje concebemos linguagens de programação de alto nível.
</small>
</center>
</div>
---  

Esses objetos são denominados de **Especificações** e representam predicados que determinam se o objeto satisfaz ou não alguns critérios. O **Código 1** ilustra uma especificação da lógica se o cliente é maior de idade. Ao extrair essa lógica para uma classe própria, amplia-se a expressividade, uma vez que o escopo da classe aponta para um único conceito; amplia-se também a testabilidade, já que essa estrutura possui poucas dependências para ser validada.

```c#
public interface ISpecification<T>
{
    bool IsSatisfiedBy(T entity);
}

public class LegalAgeSpecification: ISpecification<Customer>
{
    public bool IsSatisfiedBy(Customer entity) => 
       entity.CalculateAge() >= 18;
}
```
<p><small><b>Código 1.</b> modelo de especificação em C#</small></p>


A técnica de Especificação é um conceito poderoso em DDD. Contudo, é de certa forma frustrante ver como ela é ocultada no high level conceitual do design. Na visão de blocos de construção do DDD (ver Figura 1) ela não é destacada como os demais conceitos de modelo de domínio. Em seu livro, Evans optou por associar a técnica ao conceito de Objetos de Valor. Demais autores como Vaughn Vernon, Scott Millet e Nick Tune o seguiram nessa mesma linha.

![DDD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/r524nnqikgdekhkpttdo.png)
<p><small><b>Figura 1.</b> blocos de construção do DDD</small></p>

### Regras de negócio via Design By Contract

As Especificações não são um conceito exclusivos de Evans/Fowler. Duas décadas antes da publicação do DDD, [Bertrand Meyer](https://en.wikipedia.org/wiki/Bertrand_Meyer), um mestre em engenharia pela _École Polytechnique_ em Paris, descreveu no capítulo **Design By Contract: Building Reliable Software** em seu livro **Object-Oriented: Software Construction**, como ampliar a confiabilidade (Reliability) de software utilizando uma abordagem chamada _Design by Contract_.

Em resumo, _Design by Contract_ aplica um conjunto de regras de relacionamento entre uma classe e seus clientes (consumidores da classe), como um contrato formal composto por pré-condições e pós-condições, que expressam pra cada parte seus direitos e obrigações. Essa relações se dá por **Especificações** que visam atender o aspecto de corretude (_correctness_) da confiabilidade. Para Meyer, "Uma Especificação se dá por **Asserções**, que são expressões que envolvem e declaram predicados sobre uma entidade, que satisfaçam alguma etapa de execução do software". As asserções podem ser representadas pela seguinte **Fórmula de corretude**:

---

{% katex %}
\text{\textbraceleft P\textbraceright}
\text{ A }
\text{\textbraceleft Q\textbraceright} \\
{% endkatex %}

---

Essa formula expressa que qualquer execução de **A**, iniciada em um estado mantido por **P**, será concluída em um
estado mantido por **Q**. Vejamos o seguinte exemplo,

---

{% katex %}
\text{\textbraceleft x >= 9\textbraceright}
\text{ x := x + 5 }
\text{\textbraceleft x >=13\textbraceright}
{% endkatex %}

---

Nesse exemplo, se **P** é um número qualquer (x) maior ou igual a 9; e **A** é função cujo resultado é uma soma desse número mais o número 5; então **Q** será um número igual ou superior ao número 13. Nessa perspectiva, **"P"** representa as **pré-condições** que expressam as condições sobre as quais uma rotina deve funcionar apropriadamente; enquanto **"Q"** representa as **pós-condições**, as quais expressam o estado resultante da rotina executada.

Ainda sobre pré-condições, Meyer afirma que elas são uma **obrigação** para o cliente e um benefício para o fornecedor. Já as pós-condições, são compreendidas como benefício para o cliente e uma obrigação para o fornecedor.

## AVALIAÇÃO
Esta seção discute a implementação de regras de negócio em sistemas de informação, destacando desafios associados ao paradigma imperativo e a importância de práticas como o uso de padrões de projeto e asserções para garantir a corretude e facilidade de manutenção.

### Como desenvolvedores implementam regras de negócio nos sistemas de informação?

A resposta para essa questão está intimamente ligada as linguagens, técnicas e recursos adotados pelos programadores. Numa perspectiva geral, entendo que boa parte das principais linguagens na indústria como C, Java, Python, C# tem um forte traço no paradígma Inperativo.

---
<div>
<center>
<strong>💡SAIBA MAIS</strong>
<br/>
<small>
As linguagens imperativas são construídas em torno do conceito de mutação de estado e controle de fluxo. 
Isso significa que os programas escritos em linguagens imperativas geralmente modificam seus estados através 
de atribuições a variáveis e usam estruturas de controle como loops (for, while) e condicionais (if-else) para 
controlar a ordem de execução das instruções.
</small>
</center>
</div>
---

Apesar de uma parte dessas linguagens oferecerem suporte a outros paradígmas, o traço imperativo ainda tem muita influência sobre como os desenvolvedores escrevem seus programas. O **Código 2**, ilustrar um exemplo desse tipo de programação.

```c#
// Vineyard is a premium wine retailer that caters to both online 
// shoppers and visitors to its physical stores, offering an exquisite 
// selection of wines from around the globe. 
namespace Vineyard.Orders;

public class OrderRegistrationUseCase
{
    private const string Available = "AVAILABLE";
    private const string InTransit = "IN_TRANSIT";

    public Order RegisterOrder(OrderRegistation? command)
    {
        ArgumentNullException.ThrowIfNull(command);

        var customer = CustomerRepository.GetById(command.CustomerId);
        if (customer is null) throw new ArgumentNullException(nameof(customer));
        var isOfLegalAge = DateTime.Now.Year - customer.Birthday.Year >= 18;

        var product = ProductRepository.GetBySku(command.Sku);
        var isValidStatus = product?.Status is Available or InTransit;
        var isNotExpired = product is not null && product.DueDate < DateTime.Now;

        var discount = 0m;

        if (customer.DateOfFirstPurchase.HasValue)
        {
            if (customer.DateOfFirstPurchase < DateTime.Now.AddYears(-1))
            {
                // after 1 year, loyal customers get 10%
                discount = Math.Max(discount, .10m);
                if (customer.DateOfFirstPurchase < DateTime.Now.AddYears(-5))
                {
                    // after 5 years, 12%
                    discount = Math.Max(discount, .12m);
                }
            }
        }
        else
        {
            // first time purchase discount of 15%
            discount = Math.Max(discount, .15m);
        }

        if (customer.IsTheBirthday()) discount += 0.05m;

        var order = new Order(customer, product)
        {
            IsElegible = isOfLegalAge && isValidStatus && isNotExpired,
            Discount = discount
        };

        //TODO : Register in Database

        return order;
    }
}
```
<p><small><b>Código 2.</b> código imperativo</small></p>

O código C# apresentado anteriormente ilustra uma aplicação da _Vineyard_, uma loja fictícia especializada na venda de vinhos importados. Nesse código há uma classe chamada `OrderRegistrationUseCase` que implementa o método `RegisterOrder(OrderRegistation command)`, cuja responsabilidade é registrar a entidade `Order`. Para isso o serviço faz uso das seguintes regras:

- O `Pedido` deve conter um `Cliente`
- O `Cliente` deve ter idade igual ou superior a 18 anos
- O `Pedido` deve conter um `Produto`
- O `Produto` deve estar no status `AVAILABLE` ou `IN_TRANSIT`
- O `Produto` não pode estar vencido
- Quando a data da primeira compra do `Cliente` for superior a 1 ano, ele receberá um desconto de 10%.
- Quando a data da primeira compra do `Cliente` for superior a 5 ano, ele receberá um desconto de 12%.
- Quando for a primeira compra do `Cliente`, ele receberá um desconto de 15%.
- Se for o aniversário do `Cliente`, ele receberá mais um acréscimo de 5% de desconto.

Apesar de ilustrativo, esse código baseia-se em uma implementação real, na qual as regras de negócio estão escritas de forma imperativa no código. Isso não é algo raro, e pode ser identificado em diversos sistemas, de diferentes domínios. Nesse tipo de implementação as origens e motivações das regras de negócio que foram parar no código-fonte estão ocultas, possivelmente perdida em algum artefato estático ou na mente de algum especialista.

---
<div>
<center>
<strong>🤔 OPINIÃO</strong>
<br/>
<small>
Particularmente, enxergo o código-fonte para além de um conjunto de instruções de máquina. Ao meu ver ele deveria ser compreendido como um artefato de comunicação entre desenvolvedores na etapa de codificação. Gosto de pensar, que dentre todos os artefatos do ciclo de vida de desenvolvimento de software, o código-fonte é um dos mais dinâmicos. Talvez essas ideias sejam influenciadas por meu apreço por um movimento no qual muitos dos artefatos da engenharia de software são "as a code". 
</small>
</center>
</div>
---

### Quais práticas podem ser adotadas para codificar regras de forma a garantir a sua corretude?

Nesta seção ilustraremos o uso de algumas práticas para codificar regras de negócio no software. Para isso, faremos uso de algumas regras defenidas Meyer para o uso de Epecificações. Elas formam um conjunto de orientam a criação de Asserções e partem do princípio que "em nenhuma circustância o corpo de uma rotina deve testar suas precondições". São elas:  

- Asserções não são mecanismos para validar inputs;
- Asserções não são estruturas de controle;
- Use Asserções para escrever softwares corretos;
- Use Asserções para documentação;

#### Asserções não são mecanismos para validar inputs

As asserções devem validar apenas o escopo da regra de negócio à qual se propõe. Nesse sentido, crie módulos especializados para tratar validações de input. Uma forma de fazer isso pode ser utilizando padrões como decorator, para isolar o código que irá validar os objetos de input da rotina. o **Código 3** ilustra um exemplo de implementação.

```c#
public class OrderRegistrationValidation(IOrderRegistrationUseCase service) : IOrderRegistrationUseCase
{
    public Order RegisterOrder(OrderRegistation? input)
    {
        ArgumentNullException.ThrowIfNull(input);
        
        var customer = CustomerRepository.GetById(input.CustomerId);
        ArgumentNullException.ThrowIfNull(customer);
        
        var product = ProductRepository.GetBySku(input.Sku);
        ArgumentNullException.ThrowIfNull(product);

        var command = input with { Customer = customer, Product = product };

        return service.RegisterOrder(command);
    }
}
```

<p><small><b>Código 3.</b> exemplo de uso do padrão decorator para validação de inputs</small></p>

Ao remover as validações de input da classe `OrderRegistrationUseCase`, diminui-se sua complexidade, fazendo com que o escopo de negócio seja isolado. O **Código 4** ilustra o método `OrderRegistrationUseCase` sem as validações de input.

```c#
public class OrderRegistrationUseCase: IOrderRegistrationUseCase
{
    private const string Available = "AVAILABLE";
    private const string InTransit = "IN_TRANSIT";

    public Order RegisterOrder(OrderRegistation? command)
    {
        var customer = command?.Customer!;
        var product = command?.Product!;

        var isOfLegalAge = DateTime.Now.Year - customer.Birthday.Year >= 18;
        var isValidStatus = product.Status is Available or InTransit;
        var isNotExpired = product.DueDate < DateTime.Now;

        var discount = 0m;

        if (customer.DateOfFirstPurchase.HasValue)
        {
            if (customer.DateOfFirstPurchase < DateTime.Now.AddYears(-1))
            {
                // after 1 year, loyal customers get 10%
                discount = Math.Max(discount, .10m);
                if (customer.DateOfFirstPurchase < DateTime.Now.AddYears(-5))
                {
                    // after 5 years, 12%
                    discount = Math.Max(discount, .12m);
                }
            }
        }
        else
        {
            // first time purchase discount of 15%
            discount = Math.Max(discount, .15m);
        }

        if (customer.IsTheBirthday()) discount += .05m;

        var order = new Order(customer, product)
        {
            IsElegible = isOfLegalAge && isValidStatus && isNotExpired,
            Discount = discount
        };

        //TODO : Register in Database

        return order;
    }
}
```

<p><small><b>Código 4.</b> resultado do código sem as validações de input</small></p>

<br/>

#### Asserções não são estruturas de controle

Seguindo o princípio de que "o corpo de uma rotina não deve testar suas precondições", precisamos tornar nosso o código mais declarativo. Para isso removeremos os fluxos de validação para que eles não formem estruturas de controle.Uma das formas possíveis para executar isso é extrair as regras de negócio do método `RegisterOrder` para métodos novos na classe `OrderRegistrationUseCase`. O **Código 5** ilustra o resultado dessa extração.

```c#
public class OrderRegistrationUseCase: IOrderRegistrationUseCase
{
    private const string Available = "AVAILABLE";
    private const string InTransit = "IN_TRANSIT";

    public Order RegisterOrder(OrderRegistation? command)
    {
        var customer = command?.Customer!;
        var product = command?.Product!;

        var order = new Order(customer, product)
        {
            IsElegible = IsOrderEligible(product, customer),
            Discount = CalculateDiscount(customer)
        };

        //TODO : Register in Database

        return order;
    }

    bool IsOrderEligible(Product product, Customer customer) { ... }
    
    decimal CalculateDiscount(Customer customer) { ... }
}
```

<p><small><b>Código 5.</b>resultado do código sem as regras no corpo do método</small></p>

Após essas pequenas mudanças, o **Código 5** encontra-se bem diferente do **Código 2**. O método `RegisterOrder` da classe `OrderRegistrationUseCase` agora apresenta um escopo mais declarativo. Contudo, como garantir a corretude de nossas regras?

<br/>

#### Use Asserções para escrever softwares corretos

Com as extrações relaizadas na classe `OrderRegistrationUseCase`, foi possível remover as estruturas de controle que estavam no corpo do método, promovendo uma melhor legibilidade ao código. O próximo passo é isolar as Asserções da classe `OrderRegistrationUseCase`, tornando-as mais atômicas, reutilizáveis e passíveis de teste.Para isso, será adotado o [Business Rule Pattern](https://www.pluralsight.com/courses/c-sharp-design-patterns-rules-pattern), proposto por [Steve Smith](https://ardalis.com/blog). Estes são algums pontos importantes a serem observadas sobre o _pattern_:

- Cada regra deve seguir o **Single Responsibility Principle**.
- Para garantir que a regra siga o Princípio da Responsabilidade Única, tenha em mente o Princípio **KISS**!
- As regras podem ser ordenadas, filtradas ou agregadas conforme necessário.
- O motor de regras determina como processar as regras.
- A lógica para determinar as prioridades das regras reside no motor de regras.

O primeiro passo para implementar esse padrão, será a remoção do métodos de validação criados no **Código 5** para uma estrutura própria. O **Código 6** demonstra o resultado da transformação desses métods em Asserções da entidade `Customer` em uma classe chamada `CustomerRules`.

```c#

public class CustomerRules
{
    public class IsOfLegalAge : ISpecification<Customer>
    {
        public bool IsMatch(Customer customer) => customer.CalculateAge() >= 18;
    }
    
    public class IsFisrtPurchase : ISpecification<Customer>
    {
        public bool IsMatch(Customer customer) => customer.DateOfFirstPurchase is null;
    }  
    
    public class IsLoyalCustomers(int years) : ISpecification<Customer>
    {
        public bool IsMatch(Customer customer) => customer.DateOfFirstPurchase < DateTime.Now.AddYears(years * -1);
    }     
}
```

<p><small><b>Código 6.</b>criação das Asserções da entidade Customer</small></p>

Agora a classe `CustomerRules` possui uma maior autonomia em relação ao `OrderRegistrationUseCase`. Cada uma de suas sub-classes implementa a interface ISpecification<T> e possui comportamento livre de efeitos colaterais e são passíveis de testáveis automatizados.

Em seguida, será necessário criar a classe que fará a composição das Asserções para lidar como a regra de negócio. No Padrão _Business Rule Pattern_ esse tipo de classe é denominada **Motor de Regras** (_Rules Engine_). O **Código 7** demonstra a implementação do Motor de Regas `IsOrderEligible`, que determina se um pedido é elegível ou não.

```c#
public class IsOrderEligible
{
    private static ISpecification<Customer> LegalAgeSpecification => new CustomerRules.IsOfLegalAge();

    public static List<ISpecification<Product>> ProductSpecs  =>
    [
        new ProductRules.IsAvaliableOrInTransit(),
        new ProductRules.IsNotExpired()
    ];

    public static bool Execute(Customer customer, Product product)
    {
        if (!LegalAgeSpecification.IsMatch(customer)) return false;

        foreach (var spec in ProductSpecs)
            if (!spec.IsMatch(product)) return false;

        return true;
    }
}
```
<p><small><b>Código 7.</b> criação do Motor de Regas</small></p>

Com a extração de todas as regras que estavam acopladas ao caso de uso, o **Código 2** foi refatorado para uma versão mais declarativa focada em especificações em vez de instruções. O **Código 8** apresenta a versão final da classe `OrderRegistrationUseCase` após a refatoração.

```c#
public class OrderRegistrationUseCase : IOrderRegistrationUseCase
{
    public Order RegisterOrder(OrderRegistationCommand? command)
    {
        var customer = command?.Customer!;
        var product = command?.Product!;

        var order = new Order(customer, product)
        {
            IsElegible = IsOrderEligible.Execute(customer, product),
            Discount = CalculateDiscount.Execute(customer)
        };

        //TODO : Register in Database

        return order;
    }
}
```
<p><small><b>Código 8.</b> resultado final da classe OrderRegistrationUseCase</small></p>

### Quais as estratégias a equipe de desenvolvimento adota para gerenciar o ciclo de vida das regras no sistema? 

Reservamos esta seção para apresentar a última das regra listadas na seção anterior. Com ela abordaremos como associar os  benefícios das práticas adotadas anterirormente à prática de documentação afim de se obter uma melhor para a manutenção, compreensão e colaboração. 

<br/>

#### Use Asserções para documentação

Aproveitando o isolamento das Assertions que foram implementadas na seção anteriror, é possível usar todos os recuros  do compilador da linguagem C# como comentários e atributos para documentar as regras de negócio. Essa é uma forma de manter uma rastreabilidade  e gestão das regras de negócio que foram incluídas no projeto ao longo do tempo mais claras para o time de desenvolvimento.  Os **Código 9** e **Código 10** apresentam um exemplo de documentação das regras.

```c#
public class CustomerRules
{
    /// <summary>
    /// No Brasil, o consumo de bebidas alcoólicas por menores de 18 anos
    /// é proibido. A Lei federal 13.106/16 alterou o Estatuto da Criança
    /// e do Adolescente, tornando crime vender, fornecer, servir, ministrar
    /// ou entregar bebida alcoólica a criança ou adolescente.
    /// <see href="https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2015/lei/l13106.htm"/>
    /// </summary>
    public class IsOfLegalAge : ISpecification<Customer>
    {
        public bool IsMatch(Customer customer) => customer.CalculateAge() >= 18;
    }
    
    /// <summary>
    /// Regra de primeira compra definida pelo Gestor do Departamento de Marketing
    /// e acolhida pelo gerente de vendas durante a Black Friday
    /// de 2022.
    /// <see href="https://sistema-interno/features/98098999"/>
    /// </summary>
    [Obsolete("Regra válida até Julho de 2023")]
    public class IsFisrtPurchase : ISpecification<Customer>
    {
        public bool IsMatch(Customer customer) => customer.DateOfFirstPurchase is null;
    }  
    
    /// <summary>
    /// Implantação do programa de fidelidade após resultados da
    /// Black Friday de 2022 
    /// <see cref="IsFisrtPurchase"/>
    /// <see href="https://sistema-interno/features/99090000"/>
    /// </summary>
    public class IsLoyalCustomers(int years) : ISpecification<Customer>
    {
        public bool IsMatch(Customer customer) => customer.DateOfFirstPurchase < DateTime.Now.AddYears(years * -1);
    }     
}
```
<p><small><b>Código 9.</b> uso de comentários e attributes nas Asserções</small></p>

```c#
/// <summary>
/// Regra de desconto durante o registro do Pedido
/// Os descontos são concedidos nos seguintes casos:
/// 1 - Primeira compra = 15%
/// 2 - Cliente fidelidade com mais de 1 ano = 10%
/// 3 - Cliente fidelidade com mais de 1 ano = 12%
/// 4 - Cliente aniversariantes acréscimo de 5% ao desconto obtido
/// <see cref="CustomerRules.IsFisrtPurchase"/>
/// <see cref="CustomerRules.IsLoyalCustomers"/>
/// <see href="https://sistema-interno/features/99090000"/>
/// </summary>
public class CalculateDiscount
{
    private static List<(ISpecification<Customer> Assert, decimal Discount)> CustomerSpecs =>
    [
        (new CustomerRules.IsFisrtPurchase(), .15m),
        (new CustomerRules.IsLoyalCustomers(1), .10m),
        (new CustomerRules.IsLoyalCustomers(5), 0.12m)
    ];

    public static decimal Execute(Customer customer)
    {
        var discount = 0m;

        foreach (var spec in CustomerSpecs)
        {
            if (spec.Assert.IsMatch(customer))
                discount = Math.Max(discount, spec.Discount);
        }
        
        if (customer.IsTheBirthday()) discount += .05m;
        
        return discount;
    }    
}
```
<p><small><b>Código 10.</b> documentação de motor de regas</small></p>

Há desenvolvedores que não são favoráveis a esse tipo de documentação em código. Contudo, olhando para um contexto no qual uma equipe constantemente precisa alterar regras na aplicação e que essas regras não possuem um registro adequado ou prático, manter essa informação no código proporcionará um pouco de rastreabilidade para esse tipo de mudança, que dificilmente estará explicita em um sistema de versionamento de código.

<br/>

#### Mantenha tudo Organizado
Um último aspecto que precisa ser discutido é a organização da estrutura de código para comportar as regras de negócio.A solução aqui apresentada, foi organizada da seguinte forma:
- A classes de Asserção foram colocadas junto aos Modelos de Domínio; 
- Já os motores de regas foram colocados no escopo do caso de uso dentro de uma pasta chamada `BusinessRules`.
- Cada motor foi separado por categoria.

Em nosso exemplo, implementamos dois tipos de padrão de motor: de **Eligibility Pattern** e **Eligibility Pattern**. Tomando como referência o conceito de _Business Rule Management System (BRMS)_ apresentado no post de [Amit Tlekar](https://blogs.perficient.com/2017/09/17/10-business-rule-patterns-in-the-digital-transformation-and-cognitive-era/). Nele é apresentado outros padrões de motores de regas como _Validation Pattern, Authority and Approval Pattern, Legacy Rules Extraction,Scoring & Selection Pattern, Unstructured Big data pattern, Events pattern_ etc. A **Figura 2** ilustra a organização da final da solução.

![Projeto](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/inde33ilp2lm35n4z61t.png)
<p><small><b>Figura 2.</b> estrutura de solução</small></p>

## Conclusão

O texto discute a complexidade de definir e implementar regras de negócio em sistemas de informação dentro do campo da engenharia de software. Enfatiza a importância da colaboração entre desenvolvedores e especialistas do negócio para criar estruturas de software que atendam às exigências de qualidade e respondam adequadamente às demandas do negócio. Métodos como Business Process Management (BPM), Domain-Driven Design (DDD), e Business Capability Analysis são destacados como essenciais para mapear e expressar regras de negócio de forma efetiva.

O texto aprofunda-se em conceitos de Domain-Driven Design (DDD) e Design by Contract, propostos respectivamente por Eric Evans e Bertrand Meyer, para ilustrar abordagens na modelagem de domínios ricos e na implementação de regras de negócio com confiabilidade. As especificações são apresentadas como uma técnica para definir critérios claros e verificáveis, enquanto as asserções e contratos formais entre classes e seus consumidores são discutidos como métodos para garantir a corretude e confiabilidade do software.

Por fim, o texto aborda práticas recomendadas para codificar, documentar e manter regras de negócio nos sistemas de informação, destacando a importância de isolamento das validações, uso de asserções para corretude, e documentação adequada para facilitar a manutenção e compreensão. Estas práticas são apontadas como fundamentais para o desenvolvimento de sistemas robustos, flexíveis e alinhados com os objetivos do negócio,ressaltando a necessidade contínua de adaptação e evolução das regras de negócio diante das mudanças do mercado.

## Disclaimer

Este texto serve como uma revisão focada nos conceitos de "Design by Contract", com especial atenção à sua implementação na linguagem C#. Embora procuremos abordar os principais pontos e oferecer insights sobre como esses conceitos se aplicam ao desenvolvimento de software, é importante reconhecer que a discussão aqui apresentada não esgota a profundidade e a amplitude do "Design by Contract".

---

Caros leitores,

Espero que tenham encontrado insights valiosos na discussão sobre o design e organização de código.

Se você já experimentou essas estratégias em seus projetos, como foi? Quais vantagens e obstáculos você encontrou? Se ainda não tentou, consideraria implementá-la após essa leitura?

Seu feedback é crucial para enriquecer essa discussão.

## Referências

Você pode acessar o código-fonte desse exemplo clicando [aqui](https://github.com/yanjustino/Vineyard)

- https://softwarehut.com/blog/tech/design-patterns-rules-engine
- https://www.michael-whelan.net/rules-design-pattern/
- https://app.pluralsight.com/ilx/video-courses/48850dfa-8620-48b9-b107-9db26d07061f/7c537ceb-2b27-4a3e-8007-ae787459c4ef/a0167771-dc9a-46e2-b2d5-73c66fde13b3
- https://deviq.com/design-patterns/rules-engine-pattern
- https://blogs.perficient.com/2017/09/17/10-business-rule-patterns-in-the-digital-transformation-and-cognitive-era/
- https://martinfowler.com/bliki/BusinessReadableDSL.html


