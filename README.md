⚡ **Projeto risco relativo**

## O que é o projeto?

Este projeto é o 2 desenvolvido no bootcamp da Laboratória, onde mergulhamos em análise de dados. Durante o curso, desenvolvemos sete projetos que aplicam os conhecimentos adquiridos.

## **Contexto**

No atual cenário financeiro, a diminuição das taxas de juros tem gerado um notável aumento na demanda por crédito no banco "Super Caja". No entanto, essa crescente demanda tem sobrecarregado a equipe de análise de crédito, que atualmente está imersa em um processo manual ineficiente e demorado para avaliar as inúmeras solicitações de empréstimo. Diante desse desafio, propõe-se uma solução inovadora: a automatização do processo de análise por meio de técnicas avançadas de análise de dados. O objetivo principal é melhorar a eficiência e a precisão na avaliação do risco de crédito, permitindo ao banco tomar decisões informadas sobre a concessão de crédito e reduzir o risco de empréstimos não reembolsáveis. Esta proposta também destaca a integração de uma métrica existente de pagamentos em atraso, fortalecendo assim a capacidade do modelo. Este projeto não apenas oferece a oportunidade de se aprofundar na análise de dados, mas também proporciona a aquisição de habilidades-chave na classificação de clientes, no uso da matriz de confusão e na realização de consultas complexas no BigQuery, preparando-o para enfrentar desafios analíticos em diversos campos.

## Objetivo

"Este projeto visa automatizar e aprimorar a análise de crédito através de técnicas avançadas de análise de dados, com o intuito de aumentar a precisão na avaliação do risco de crédito. Isso possibilitará ao banco tomar decisões mais informadas e reduzir a incidência de empréstimos incobráveis. O objetivo principal é melhorar a eficiência e precisão na avaliação do risco de crédito, permitindo ao banco tomar decisões embasadas sobre concessões de crédito e mitigar o risco de empréstimos não reembolsáveis. Além disso, a proposta ressalta a integração de uma métrica existente de pagamentos em atraso, fortalecendo assim a capacidade do modelo."


<details>
  <summary><strong>Obtenção dos dados</strong></summary>
  
  <p>Para esse projeto foi disponibilizado pela Laboratoria 4 tabelas.</p>
  
  <h4>Tabela user_info</h4>
  
  <table>
    <thead>
      <tr>
        <th>Arquivo</th>
        <th>Variável</th>
        <th>Descrição</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>user_info</td>
        <td>user id</td>
        <td>Número de identificação do cliente (único para cada cliente)</td>
      </tr>
      <tr>
        <td></td>
        <td>age</td>
        <td>Idade do cliente</td>
      </tr>
      <tr>
        <td></td>
        <td>sex</td>
        <td>Gênero do cliente</td>
      </tr>
      <tr>
        <td></td>
        <td>last month salary</td>
        <td>Último salário mensal que o cliente informou ao banco</td>
      </tr>
      <tr>
        <td></td>
        <td>number dependents</td>
        <td>Número de dependentes</td>
      </tr>
    </tbody>
  </table>
  
  <h4>Tabela loans_outstanding</h4>
  
  <table>
    <thead>
      <tr>
        <th>Arquivo</th>
        <th>Variável</th>
        <th>Descrição</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>loans_outstanding</td>
        <td>loan id</td>
        <td>Número de identificação do empréstimo (único para cada empréstimo)</td>
      </tr>
      <tr>
        <td></td>
        <td>user id</td>
        <td>Número de identificação do cliente</td>
      </tr>
      <tr>
        <td></td>
        <td>loan type</td>
        <td>Tipo de empréstimo (real state = imóveis, others = outros)</td>
      </tr>
    </tbody>
  </table>
  
  <h4>Tabela loans_detail</h4>
  
  <table>
    <thead>
      <tr>
        <th>Arquivo</th>
        <th>Variável</th>
        <th>Descrição</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>loans_detail</td>
        <td>user id</td>
        <td>Número de identificação do cliente</td>
      </tr>
      <tr>
        <td></td>
        <td>more 90 days overdue</td>
        <td>Número de vezes que o cliente apresentou atraso superior a 90 dias</td>
      </tr>
      <tr>
        <td></td>
        <td>using lines not secured personal assets</td>
        <td>Quanto o cliente está utilizando em relação ao seu limite de crédito, em linhas que não são garantidas por bens pessoais, como imóveis e automóveis</td>
      </tr>
      <tr>
        <td></td>
        <td>number times delayed payment loan 30 59 days</td>
        <td>Número de vezes que o cliente atrasou o pagamento de um empréstimo (entre 30 e 59 dias)</td>
      </tr>
      <tr>
        <td></td>
        <td>debt ratio</td>
        <td>Relação entre dívidas e ativos do cliente. Taxa de endividamento = Dívidas / Patrimonio</td>
      </tr>
      <tr>
        <td></td>
        <td>number times delayed payment loan 60 89 days</td>
        <td>Número de vezes que o cliente atrasou o pagamento de um empréstimo (entre 60 e 89 dias)</td>
      </tr>
    </tbody>
  </table>
  
  <h4>Tabela default</h4>
  
  <table>
    <thead>
      <tr>
        <th>Arquivo</th>
        <th>Variável</th>
        <th>Descrição</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>default</td>
        <td>user id</td>
        <td>Número de identificação do cliente</td>
      </tr>
      <tr>
        <td></td>
        <td>default flag</td>
        <td>Classificação dos clientes inadimplentes (1 para clientes já registrados alguma vez como inadimplentes, 0 para clientes sem histórico de inadimplência)</td>
      </tr>
    </tbody>
  </table>
</details>



## Conclusões

1. **Risco Relativo por Idade:**
Com base no risco relativo, os jovens entre 21 e 42 anos apresentam um risco maior de inadimplência. Além disso, pessoas de até 52 anos também demonstram um risco elevado de não pagamento.
2. **Risco Relativo por Número de Empréstimos:**
Pessoas com um maior número de empréstimos ativos têm uma probabilidade mais alta de serem maus pagadores.
3. **Atrasos nos Pagamentos:**
Pessoas que frequentemente atrasam seus pagamentos por mais de 90 dias tendem a apresentar uma maior inadimplência com o banco.
4. **Matriz de confusão:**
Os resultados mostram que o modelo tem uma acurácia balanceada de 76%, indicando que, em média, está classificando corretamente 76% das amostras. No entanto, o baixo F1-score de 15% sugere que o modelo está tendo dificuldades em alcançar um equilíbrio entre precisão e recall. Para melhoria futura, podemos explorar diferentes modelos ou ajustar os parâmetros.
5. **Regressão logistica**
Após análise, constatamos que clientes com um score a partir de 5 tendem a apresentar um maior risco de inadimplência.

## 🛠 Tecnologias

- BigQuery (linguagem SQL): para gerenciamento de dados.
- Google Colab (Python): para realizar análises em Python.
- Looker Studio: para visualização de dados.

## Dashboard no Looker Studio

[Clique Aqui](https://lookerstudio.google.com/u/0/reporting/37a47ef1-d2dc-4b94-a901-937abe99f296/page/p_x96g2a0hhd/edit)

![](https://github.com/Mayara-alvess/Risco_relativo/blob/main/03projeto/imagem.jpg)

### Risco relativo
![](https://github.com/Mayara-alvess/Risco_relativo/blob/main/03projeto/risco%20relativo%20imagem.png)


### Matriz de confusão feita no google colab
[Clique Aqui](https://colab.research.google.com/drive/16QL6dYXDCcbo-Hl9cSrG5X3Pbn-iyw9N?authuser=1#scrollTo=rmYuiYnw4U2b)

![](https://github.com/Mayara-alvess/Risco_relativo/blob/main/03projeto/matrizz.png)

### Regressão logistica
![](https://github.com/Mayara-alvess/Risco_relativo/blob/main/03projeto/regressão_logistica.png)

## Diretório

- Na pasta de 03projeto, você encontrará as consultas realizadas através do BigQuery, bem como o ambiente do Google Colab, onde executei os códigos em Python.
- Para uma documentação mais detalhada, você pode acessar a página no Notion [clique aqui](https://www.notion.so/Documenta-o-risco-relativo-2c8c2ce696c44aedb7f150d864aa38c8?pvs=21)
