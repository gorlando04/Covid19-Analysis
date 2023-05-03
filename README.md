# Análise de dados sobre o COVID-19 ao redor do mundo utilizando MongoDB

Este projeto tem como objetivo responder algumas perguntas propostas sobre o covid-19. Os dados que serão utilizados foram coletados diretamente da OMS e representam o mundo todo, sendo separados por países ou continentes.
Para a realização da análise foi utilizado o MongoDB junto ao python, que permite uma boa análise, e um bom entendimento de como funciona os bancos de dados NoSQL. O interessante deste projeto é compreender a rapidez e liberdade que o 
MongoDB permite.

Em relação a instalação, tudo foi realizado utilizando um jupyter-notebook conectado a um Cliente do MongoDB que foi instalado na máquina local. Toda a instalação e configuração do Cliente está diponível na documentação do MongoDB,
que possui uma exmplicação muito cautelosa e atenciosa. 

https://www.mongodb.com/

# Criando o Cliente do MongoDB

Para que o cliente consiga ser inicializado, é necessário ter o mongo instalado e o comando para incializar é : **sudo systemctl start mongod**

Após, isto deve-se importar a biblioteca do python **pymongo** que realiza a interação entre o Cliente do MongoDB e o o notebook do python, facilitando a interface.

Para isto, os seguintes comandos foram utilizados:

![Screenshot from 2023-05-03 10-37-31](https://user-images.githubusercontent.com/91696970/235982372-84198aa1-fb24-42bc-85af-91d64d26b21d.png)

![Screenshot from 2023-05-03 10-37-47](https://user-images.githubusercontent.com/91696970/235982374-358eff78-bb56-4f1b-9830-84bc5a275136.png)

Após isto, pode-se inicializar a análise de dados.

# Conjunto de dados

Para importar o conjunto de dados que está sendo analisado deve-se seguir os seguintes passos:

Abrir o seguinte link: https://github.com/owid/covid-19-data/tree/master/public/data/

Este link leva direto para o repositório no GitHub do sistema que coleta as informações sobre o Covid-19 no mundo.
Após isso, deve-se clicar no formato do arquivo que deseja ser baixado, nesse caso foi o formato '.csv'.
Após isso, deve-se move o arquivo para a área em que será feita a análise (Collab,Jupyter-Notebook,Jupyter-Lab, etc...)

![Screenshot from 2023-05-03 10-39-21](https://user-images.githubusercontent.com/91696970/235982693-821c77eb-cfd3-4390-8e9f-4a006d762031.png)

O conjunto de dados, no momento da análise, continha 200 mil linhas e 67 atributos, é necessário enfatizar o momento, pois este dataset é incrementativo, ele vai aumentando conforme os dados
em relação ao Covid vao evoluindo.

Como o dataset possui muitos atributos apenas os que serão utilizados nesta análise serão analisados/descritos.

| Nome atributo | Explicação |
| --- | --- |
| Location | País. Entretando caso seja referente a um continente, a localização é o continente |
| Total deaths | Total de mortes da localização |
| Total tests | Total de testes feitos na localidade |
| Total vaccinations | Total de vacinas aplicadas na localidade |
| Continent | Continente da localidade |
| Total cases | Total de casos de Covid-19 na localização |
| Median age | Mediana da idade da localidade |

# Perguntas a serem respondidas

Nesta análise de dados, as seguintes perguntas foram propostas:

A primeira análise que deverá ser feita é: Listar o nome dos países, seu continente e o total de testes de covid realizados, o total de mortos por covid e o número de vacinados por covid.

A segunda análise que deverá ser feita é: Listar o nome dos continentes e exiba o total de testes de covid realizados, o total de mortos por covid e o número de vacinados por covid. 

A terceira análise que deverá ser feita é: Existe alguma relação entre a média de idade dos moradores dos países e a taxa de mortalidade? Crie uma consulta que permita relacionar essas duas informações.

# Pré-processamento

Antes de inicia a análise dos dados, é necessário arrumar um pouco os dados. A primeira será realizada pelo fato da definição das perguntas. Como se deseja construir os dados por continente e não pegar pronto, quando uma localidade se referir
a um continente ela será removida para que o trabalho possa ser feito com auxilio do MongoDB

Para isto, basta remover as linhas onde o continente é nulo, visto que nesse caso a localidade será o continente. Após isto, podemos adiciolnar os valores a coleção do MongoDB


# Adicionando valores a coleção

O MongoDB possui uma arquitetura única quando se trata de banco de dados. É formado por coleções e documentos. As coleções são regiões que os dados são armazenados enquanto os documentos são os exemplos que serão adicionados. Uma coisa
interessante de se notar é que os documentos do MongoDB são semelhantes aos dicionários em Python, devem ser adicionados em formato .json, dessa maneira, devemos transformar o arquivo csv baixado em dicionário e após isso inserir os dados 
na coleção **Covid-19** do MongoDB.

Para isso criamos uma lista de dicionários contendo os dados do conjunto de dados, e em seguida inserimos os dicionários na coleção do MongoDB e fazemos a verificação para descobrir se os dados foram todos inseridos corretamente,
por meio de uma simples comparação entre o tamanho da coleção e o tamanho do dataframe lido anteriormente.

# Primeira análise

Como dito anteriormente, este conjunto de dados possui uma particularidade específica, possui dados incrementais, ou seja, existem diversas linhas com a localidade sendo Brazl, mas essas linhas representam momentos diferentes e podem
conter valores diferentes. Desta maneira, uma solução para conseguir encontrar os dados totais foi necessário pegar o valor máximo que aquela localidade registrou. A data mais recente não foi utilizada, pelo simples fato de às vezes na data
máxima poderia haver valores NULOS e desta forma prejudicar a análise.

Assim, a primeira análise que era referentea listagem do nome dos países, total de testes,mortes e vacinados, foi realizada da seguinte forma.

![Screenshot from 2023-05-03 11-09-46](https://user-images.githubusercontent.com/91696970/235989937-0e2dbd68-3ed1-46cb-b06e-4d22d3be6043.png)

O resultado está diponível no notebook do jupyter e não será exibido pois como se trata de diversos países é bem extenso.

# Segunda análise

A segunda análise era referente à listagem dos continentes em relação ao número de mortes, vacinados e testes totais. Nota-se que a ideia dessa análise não era pegar os continentes já preparados, mas sim realizar um agrupamento entre as 
localidades para que fosse possível utilizar o MongoDB.

Assim, seguindo a lógica utilizada, pode-se construir a seguinte consulta:


![Screenshot from 2023-05-03 11-13-40](https://user-images.githubusercontent.com/91696970/235990981-389cbe25-a41d-457f-acfb-3212e3681302.png)


# Terceira análise

Para a realização da terceira análise, que consistia em calcular a taxa de mortalidade e visualizar uma relação com a mediana da idade no país. 

Para esta tarefa, a ideia era pegar o total de mortes e dividir pelo total de casos, entretanto em um único caso um problema aconteceu, o total de mortes era maior que o total de casos. Isso parece algo bem incoerente de acontecer, e de 
fato é, mas quando se verificou qual país era pode-se construir uma explicação. O país em questão era a Coreia do Norte, que é bem fechada em relação ao restante do mundo e desta maneira, seus dados podem as vezes ser obtidos de forma não
tão correta, levando a dados um pouco incoerentes.

Desta maneira, a seguinte consulta pode-se se construída para realizar a visualização dos dados:

![Screenshot from 2023-05-03 11-17-54](https://user-images.githubusercontent.com/91696970/235991854-3bd1d564-de90-496b-89ae-583b61196065.png)


Após isto, foi possível construir o gráfico para visualizar esta relação.


![image](https://user-images.githubusercontent.com/91696970/235991939-4c5c2ec2-322b-4fb4-9cb5-adeb9fddc1df.png)



# Conclusão

Após a resposta das perguntas propostas, pode-se concluir que esta análise foi proveitosa. Mesmo não sendo muito extensa permitiu que o funcionamento do MongoDB fosse compreendido melhor e como a integração com o Python pode ser benefica
caso estejamos trabalhando com grandes conjuntos de dados que precisam ser analisados de maneira rápida e eficiente.

Em relação aos resultados, pode-se perceber claramente que a mortalidade dos países está diretamente relacionada a política que foi implementada no país em relação ao covid-19, como por exemplo, no Brasil, que foi um dos países em que mais
houve mortes, a política do covid-19 não foi tão estrita e o governo não implementou medidas sanitárias mais restritivas, como as que foram implementadas em países nórdicos por exemplo.

Entretanto, ao anaisar os resultados da segunda pergunta, percebe-se que o número de mortes está muito relacioando com a quantidade da população do local, o que de certa forma é intuitivo de se pensar.













