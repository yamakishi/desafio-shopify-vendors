# Desafio Shopify — Vendedores via Metaobjects

Tema Shopify desenvolvido para renderizar dinamicamente os vendedores de uma loja e os produtos associados a cada vendedor.

A implementação utiliza Metaobjects da Shopify, Liquid, HTML e CSS responsivo. Não existem vendedores cadastrados manualmente no schema e nenhum JavaScript é necessário para o funcionamento da seção.

## Links

- [Repositório no GitHub](https://github.com/yamakishi/desafio-shopify-vendors)
- [Preview da development store](https://challenge-shopif.myshopify.com?preview_theme_id=165576704220)
- [Theme Editor](https://challenge-shopif.myshopify.com/admin/themes/165576704220/editor)

> O preview pode solicitar a senha da development store ou autenticação, dependendo das permissões da loja.

## Objetivo

A seção `ec-vendors-metaobject` busca automaticamente todos os vendedores ativos do Metaobject `store_vendor` e apresenta:

- Foto do vendedor;
- Nome;
- Cargo;
- Produtos vendidos;
- Imagem e link dos produtos relacionados.

A seção pode ser adicionada em qualquer template compatível por meio do Theme Editor.

## Metaobject utilizado

O projeto espera um Metaobject com a seguinte configuração:

| Propriedade | Valor          |
| ----------- | -------------- |
| Nome        | `Vendedor`     |
| Type        | `store_vendor` |

Campos esperados:

| Campo               | Tipo                             |
| ------------------- | -------------------------------- |
| `nome`              | Texto de linha única             |
| `cargo`             | Texto de linha única             |
| `foto`              | Arquivo ou mídia                 |
| `produtos_vendidos` | Lista de referências de produtos |

Os registros são consumidos diretamente no Liquid:

```liquid
{% assign vendors = metaobjects.store_vendor.values %}
```

Os produtos são obtidos como objetos `Product` reais por meio de:

```liquid
{% for product in vendor.produtos_vendidos.value %}
  <!-- Produto relacionado -->
{% endfor %}
```

Não é necessário filtrar manualmente registros em rascunho. `metaobjects.store_vendor.values` disponibiliza os registros publicados acessíveis à storefront.

## Arquivos implementados

### `sections/ec-vendors-metaobject.liquid`

Section principal responsável por:

- Consultar os vendedores no Metaobject;
- Renderizar os cards dinamicamente;
- Tratar vendedores sem foto;
- Tratar vendedores sem produtos;
- Tratar produtos sem imagem;
- Gerar imagens responsivas e otimizadas;
- Controlar o grid para desktop e mobile;
- Expor configurações no Theme Editor;
- Evitar conflitos quando a section é usada mais de uma vez na página.

### `assets/ec-vendor-placeholder.svg`

Avatar padrão exibido quando o vendedor não possui foto.

### `assets/ec-vendor-badge.svg`

Ícone decorativo utilizado nos cards, extraído da referência visual do Figma.

## Configurações no Theme Editor

A section disponibiliza as seguintes configurações:

| Configuração               | ID                | Opções         |
| -------------------------- | ----------------- | -------------- |
| Título                     | `title`           | Texto livre    |
| Subtítulo                  | `subtitle`        | Texto livre    |
| Itens por linha no desktop | `columns_desktop` | 1 a 4          |
| Itens por linha no mobile  | `columns_mobile`  | 1 ou 2         |
| Cor da borda               | `border_color`    | Seletor de cor |
| Fundo do card              | `card_background` | Seletor de cor |
| Espaçamento interno        | `card_padding`    | 16 a 48px      |

O valor padrão para mobile é uma coluna, proporcionando a experiência mais confortável em telas pequenas.

## Responsividade

O grid usa `minmax(0, 1fr)` e adapta a quantidade efetiva de colunas quando a preferência configurada produziria cards estreitos demais.

### Breakpoints

- A partir de `1200px`: respeita a configuração desktop de 1 a 4 colunas;
- Entre `900px` e `1199px`: a preferência de 4 colunas é reduzida para 3;
- Entre `750px` e `899px`: as preferências de 3 ou 4 colunas são reduzidas para 2;
- Até `749px`: utiliza a configuração de colunas mobile;
- Até `749px` com 2 colunas: utiliza o layout compacto específico para mobile.

Além dos breakpoints de viewport, os cards possuem uma container query baseada na largura real disponível:

- Cards com até `300px` reorganizam avatar, badge, nome e cargo;
- Nome e cargo passam a utilizar uma linha própria quando necessário;
- Avatar, badge, tipografia, padding e miniaturas são reduzidos de maneira controlada;
- Produtos excedentes fazem wrap e permanecem dentro do card.

Essa abordagem também protege combinações como 3 colunas em viewports intermediários e uma coluna em celulares muito estreitos.

## Tratamento de estados vazios

A implementação não gera erro quando:

- Não existem vendedores publicados;
- Um vendedor não possui foto;
- Nome ou cargo estão vazios;
- Um vendedor não possui produtos relacionados;
- Um produto relacionado não possui imagem destacada.

Quando não existem vendedores, o grid não é renderizado. O título e o subtítulo permanecem visíveis, caso estejam configurados.

## Imagens e acessibilidade

- Fotos e produtos utilizam `image_url` e `image_tag`;
- Imagens usam dimensões adequadas ao layout;
- O carregamento utiliza `loading="lazy"`;
- Imagens possuem textos alternativos coerentes;
- Links de produtos possuem nomes acessíveis;
- O avatar padrão e o badge decorativo não duplicam informações para tecnologias assistivas;
- Nome e cargo quebram naturalmente entre palavras, sem `word-break: break-all`;
- Estados de foco são visíveis nos links de produtos.

## Como executar localmente

### Pré-requisitos

- Node.js;
- Shopify CLI;
- Acesso à development store utilizada pelo projeto.

Autentique a Shopify CLI e execute:

```bash
shopify theme dev
```

Para validar o tema:

```bash
shopify theme check
```

Resultado da última validação realizada durante a implementação:

```text
40 files inspected with no offenses found.
```

## Como adicionar a seção

1. Abra o editor de temas da Shopify;
2. Entre no template desejado;
3. Clique em **Adicionar seção**;
4. Selecione **Vendedores**;
5. Configure título, subtítulo, colunas, cores e padding;
6. Salve as alterações.

Os vendedores não são configurados por blocos. Para adicionar, remover ou editar vendedores, altere os registros do Metaobject `store_vendor` no Admin da Shopify.

## Tecnologias

- Shopify Liquid;
- Metaobjects;
- CSS Grid;
- Flexbox;
- Container Queries;
- Shopify Theme Editor;
- Shopify CLI e Theme Check.

## Referência visual

O layout foi desenvolvido com base no mock fornecido no Figma. Foram reproduzidos os principais elementos visuais, incluindo grid, espaçamentos, bordas, cores, raio dos cards, avatar, badge e miniaturas de produtos, com adaptações responsivas para preservar legibilidade e evitar overflow.
