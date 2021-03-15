---
layout: post
title:  "Um rápido exemplo de monkeypatching"
date:   2020-12-15
categories: Programming
---
Testes é um assunto bem abrangente e complicado. Em linhas gerais, testamos
para garantir que nossas funcionalidade funcionem. Existem vários tipos de teste que usamos para isso:
- testes funcionais
- testes unitários
- testes de regressão
- e muitos outros.


Irei mostrar aqui dois exemplos de testes unitários que podemos nos deparar um dia.
Quando escrevemos uma função, digamos, `fatorial`, é relativamente fácil testar a sua saída.
Testamos um valor qualquer, por exemplo, que o fatorial de 4 é 24, e testamos os casos extremos, como
fatorial de um número negativo e a base. Por exemplo:

```python

def fat(n: int):
    return 1 if n == 1 else n * fat(n-1)

class TestFat(unittest.TestCase):
    def test_fat_stop_case(self):
        self.assertEqual(fat(1), 1)

    def test_fat_of_3_equals_6(self):
        self.assertEqual(fat(3), 9)
```

Aqui está faltando o fatorial de um número negativo. Para isso, precisamos definir claramente qual vai ser a saída. Será uma exceção? Será um booleano? Aqui vou retornar apenas um booleano, então vamos refatorar nosso fatorial, adicionando mais uma condição. Primeiro vamos aos testes.
python
```python
clas TestFat(unittest.TestCase):
    [...]

    def test_negative_fat_is_False(self):
        self.assertEqual(fat(-1), False)

```
E para a função `fat`:

```python
def fat(n: int):
    if n >= 1:
        return 1 if n == 1 else n * fat(n-1)
    return False
```
Tranquilo né? Isso claramente não demonstra que o programa funciona para todas as entradas, mas já é um bom indicador de que está funcionando para o que queremos.

Caso alguém mude a implementação, para otimizar o processamento, por exemplo, todos os testes ainda devem funcionar. Essa técnica é muito importante, e é conhecida como teste de regressão.

A maior vantagem dos testes é que não precisamos mais rodar todos os nossos inputs na mão, nos salvando um tempo mecânico, que é muito "error prone", além de consumir um tempo muito tedioso. Além disso, o funcionamento do programa pode ser garantido parcialmente(requer bons testes e que abrangem uma boa parte do código) através dos testes de regressão depois das refatorações.

Mas vamos então ao *monkeypatching*, o intuito do post de hoje.

---

De acordo com a [Wikipedia], monkey patch é uma forma do programa extender ou modificar programas do sistema localmente, afetando apenas a instância do programa rodando.

O que isso quer dizer e por que precisamos?

Quando estamos acessando ferramentas externas ao nosso código, corremos o risco de fazer um teste não passar devido a fatores que não são de implementação. Por exemplo, digamos que você quer acessar uma API do Coronavírus. Você desenvolveu o teste comunicando com a API e testou várias vezes, mas de repente parou de funcionar. Seria algum erro que você mexeu em alguma parte do código ou um outro problema que está fora do nosso controle, como uma perda de conexão?

Para isso, usamos essa técnica conhecida como monkey patching. Alteramos a estrutura da resposta da conexão para ela sempre retornar uma resposta previsível, e assim executamos nossos testes adequadamente, sem depender de entidades externas.

No Python, temos a famosa biblioteca de teste unitário, a `unittest`. Vamos usar ela para elaborar os testes.

No primeiro cenário para demonstrar o uso do patching, irei criar um simples programa que lida com a criação de arquivos no computador.

Para a classe do arquivo, pensei no funcionamento dela da seguinte forma:
    - recebe um diretório como parâmetro inicial da classe
    - tem uma função de criar arquivo
    - uma função de escrever um texto no arquivo criado anteriormente
    - e outra que recebe o nome do arquivo e o texto e salva os dados
    - outras funções auxiliares como de colocar extensao .txt se não informar no nome do arquivo


Dito isso, vamos aos testes.

```python


class FileHandlerTest(unittest.TestCase):
    def setUp(self):
        """
        setando o diretorio para um lugar existente
        """
        self.dir = './mydir/'
        self.file_handler = MyFile(self.dir)

    def test_get_dir_name(self):
        """
        testa se o nome do diretorio foi setado
        """
        self.assertEqual(self.file_handler.get_dir_name, self.dir)

    def test_add_txt_to_plain_text_input(self):
        """
        testa se adicionou a extensão
        """
        self.assertEqual(
            self.file_handler.add_txt_extension('file'),
            "file.txt")
```
Bom, dois testes criados. O código que desenvolvi foi o seguinte
```python
class MyFile(object):
    def __init__(self, dir_name):
        self.set_dir_name(dir_name)
        self._last_created = None  # salva o ultimo arquivo criado

    @property
    def get_dir_name(self):
        return self._dir_name


    def set_dir_name(self, dir_name: str):
        """
        """
        self._dir_name = dir_name if dir_name.endswith('/') else dir_name + '/'

    def add_txt_extension(self, file_name: str):
        return file_name + '.txt'
```
Por enquanto nada de novo, nenhuma comunicação externa que causa efeito colateral.

Mas e para criar um arquivo? Seria boa ideia ficar criando arquivos aleatórios no computador só para testar? Para esse problema, usamos o `patch` do python, que fica na biblioteca `unittest.mock`. outra função que usamos é a `mock_open`, função que simula a manipulação de arquivos.

O nosso teste, que comentei no arquivo para facilitar o entendimento, fica o seguinte

```python
class FileHandlerTest(unittest.TestCase):
    [...]

    def test_create_file(self):
        m = mock_open()
        # cria_pasta é o nome do arquivo .py
        # .open é a função que estamos simulando
        with patch('cria_pasta.open', m):
            # essa função utiliza a função open para criar um arquivo
            self.file_handler.create_file('abc')
            m.assert_called_once()  # assegura que open foi chamado apenas uma vez
            m.assert_called_with(self.dir + 'abc.txt', 'w')  # assegura que open foi chamado com esses argumentos
```

Você pode olhar a [documentação] do Python para entender melhor esses exemplos, mas perceba que passamos o nome da função que estamos simulando para a função patch()

A função do arquivo é a seguinte
```python
class MyFile(object):
    [...]

    def create_file(self, file_name: str) -> str:
        # add txt if file does not have extension
        if not file_name.endswith(r'(\.).*'):
            file_name = self.add_txt_extension(file_name)
        file: str = self._dir_name + file_name
        with open(file, 'w') as _:  # cria o arquivo
            self._last_created: str = file
        return file
```

Estão faltando duas funções, a de escrever no arquivo, e a outra que é de criar o arquivo e escrever:
```python
    def test_write_created_file(self):
        m = mock_open()
        with patch('cria_pasta.open', m):
            self.file_handler.create_file('abc')  # cria um arquivo
            self.file_handler.write_to_file('My text')  # escreve no arquivo 'abc'
            m().write.assert_called_with('My text')  # assegura que a função write() foi chamada com o texto passado

    def test_create_and_write_file(self):
        m = mock_open()
        with patch('cria_pasta.open', m):
            self.file_handler.write_to_file(('README.md', '# READ ME'))  # escreve READ ME no arquivo README.md
            m.assert_called_with(self.file_handler.get_dir_name + 'README.md', 'a')  # assegura que a função open foi chamada com o nome correto do arquivo
            m().write.assert_called_with('# READ ME')  # assegura que a escrita do arquivo é a mesma passada na função

```
Por fim, precisamos implementar essas funções!
```python
    @singledispatchmethod
    def write_to_file(self, text: str):
        with open(self._last_created, 'a') as f:
            f.write(text)


    @write_to_file.register(tuple)
    def _(self, arg: tuple):
        file_name: str = arg[0]
        text:str = arg[1]
        with open(self._dir_name + file_name, 'a') as f:
            f.write(text)
```
Aqui criamos a mesma função para inputs diferentes, caso você se interesse por esse assunto em Python, pesquise pela decorator `singledispatchmethod` [aqui].

Para finalizar, deixarei um teste com uma API. A idéia é bem parecida com o que mostrei aqui, então acredito que com o conhecimento apresentado já dá para entender bem.

Obrigado pela leitura!


Classe principal:
```python
import json
import requests
import pandas as pd
from collections import namedtuple


def read_json(data):
    response = json.dumps(data['data'])
    return pd.read_json(response)


class CovidApi(object):
    def __init__(self):
        self.URL = 'https://covid-api.com/api'

    def _get(self, action: str):
        """
        action is one of:
          - /action/
          - /action/action/

        last slash is optional
        """
        return requests.get(f'{self.URL}{action}')

    def get_reports(self):
        return self._get('/reports/').json()

    def get_total_reports(self):
        return self._get('/reports/total').json()


    def get_by_region(self, country_iso: str):
        df = read_json(self._get(f'/reports/?iso={country_iso}').json())
        deaths = int(df['deaths'].sum())
        confirmed = int(df['confirmed'].sum())
        recovered = int(df['confirmed'].sum())
        return {
            'deaths': deaths,
            'confirmed': confirmed,
            'recovered': recovered
        }

```
Classe de teste:
```python
import unittest
from unittest.mock import patch, Mock
from conexao_api import CovidApi


class CovidApiTest(unittest.TestCase):

    @patch('conexao_api.requests.get')
    @patch('conexao_api.read_json')
    def test_get_by_region(self, mock_get, mock_pandas):
        return_value = {'deaths': 1,
                                  'confirmed': 1,
                                  'recovered': 2
                                  }

        mock_get.return_value.json = return_value
        mock_pandas.return_value.read_json = return_value
        c = CovidApi()
        c.get_by_region('BRA')
        mock_get.assert_called_once()
if __name__ == "__main__":
    unittest.main()

```
[aqui]: https://docs.python.org/3/library/functools.html
[documentacao]: https://docs.python.org/3/library/unittest.mock.html
[Wikipedia]: https://en.wikipedia.org/wiki/Monkey_patch
