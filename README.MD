# Buscador de Cursos (Alura) – PHP + Composer

Pequeno projeto didático que usa **Composer**, **Guzzle** e **Symfony DomCrawler** para buscar os nomes dos cursos de PHP no site da **Alura** e exibir no terminal.

---

## ✅ O que o projeto faz

- Faz uma requisição HTTP para uma página da Alura (base: `https://www.alura.com.br/`).
- Carrega o HTML com o **DomCrawler** e filtra os elementos de cursos (seletor `span.card-curso__nome`).
- Retorna uma **lista de strings** com os nomes dos cursos.
- Mostra os nomes no terminal.

---

## 🧱 Stack e dependências principais

- **PHP** (8.2+ recomendado – exigido pelo `symfony/dom-crawler:^7`)
- **Composer**
- **guzzlehttp/guzzle:^7.10**
- **symfony/dom-crawler:^7.3** e **symfony/css-selector:^7.3**
- **phpunit/phpunit:^12.3** (tests)
- **squizlabs/php_codesniffer:^4.0** (PSR-12)
- **phan/phan:^5.5** (análise estática)

---

## 📁 Estrutura do projeto

```
buscador_cursos_alura/
├─ .phan/config.php              # Configuração do Phan (inclui vendor para indexação)
├─ buscar-cursos.php             # Script CLI que executa o buscador e imprime os cursos
├─ composer.json                 # Autoload PSR-4: Alura\BuscadorDeCursos\ => src/
├─ functions.php                 # Função exibeMensagem()
├─ src/
│  └─ Buscador.php               # Classe Buscador (Guzzle + DomCrawler)
├─ tests/
│  └─ TestBuscadorDeCursos.php   # Teste unitário (mocks de Guzzle/Response/Stream)
└─ vendor/                       # Dependências do Composer
```

**Namespace:** `Alura\BuscadorDeCursos` (mapeado para `src/` via PSR-4).

---

## ⚙️ Requisitos

- PHP **8.2+** (necessário para `symfony/dom-crawler:^7`)
- Composer

Verifique sua versão:
```bash
php -v
composer -V
```

---

## 🚀 Instalação

Na raiz do projeto:

```bash
composer install
```

> Se instalar o **Phan** e quiser melhor performance, instale a extensão `php-ast` ou use a flag `--allow-polyfill-parser` (ver seção de *Qualidade de Código*).

---

## ▶️ Uso

Execute o script principal:

```bash
php buscar-cursos.php
```

Isso vai:
1. Construir um `GuzzleHttp\Client` com `base_uri` = `https://www.alura.com.br/`.
2. Instanciar `Symfony\Component\DomCrawler\Crawler`.
3. Chamar `Alura\BuscadorDeCursos\Buscador::buscar('/cursos-online-programacao/php')`.
4. Imprimir cada curso encontrado.

> **Observação:** O seletor CSS `span.card-curso__nome` depende do HTML da Alura. Se a página mudar, pode ser necessário ajustar o seletor.

---

## 🧪 Testes

Você pode rodar os testes diretamente pelo Composer (script já incluso):

```bash
composer test
```

ou diretamente:
```bash
vendor/bin/phpunit tests/TestBuscadorDeCursos.php
```

O teste cria *mocks* de `ClientInterface`, `ResponseInterface` e `StreamInterface`, injeta HTML de exemplo e valida que o `Buscador` retorna 3 cursos.

---

## 🧹 Qualidade de código

### Code Style (PSR-12)

```bash
composer cs
# ou diretamente:
vendor/bin/phpcs --standard=PSR12 src/
```

### Análise Estática (Phan)

```bash
composer phan
# ou diretamente:
vendor/bin/phan --allow-polyfill-parser
```

O arquivo **.phan/config.php** já inclui `src` e algumas libs do `vendor` na `directory_list` para indexação de tipos.  
Se preferir performance máxima com Phan:
```bash
pecl install ast
# no php.ini:
# extension=ast.so
```

Há também um atalho para rodar tudo:
```bash
composer check   # roda @test, @cs e @phan
```

---

## 🔧 Como funciona internamente

### `src/Buscador.php`
- Recebe `ClientInterface` (Guzzle) e `Crawler` por injeção de dependência.
- Método `buscar(string $url): array`:
    1. `request('GET', $url)`
    2. `addHtmlContent($html)`
    3. `filter('span.card-curso__nome')`
    4. Extrai `textContent` e retorna um `array` de strings.

### `buscar-cursos.php`
- Monta `Client` e `Crawler`, instancia `Buscador` e imprime os cursos via `exibeMensagem()`.

---
