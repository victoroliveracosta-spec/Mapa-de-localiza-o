# Alphacampus — Localizador de memórias

Site estático em português, com busca por nome, idade, falecimento, quadra, setor e jazigo. Inclui logo e 35.295 registros extraídos da planilha de 27/10/2026. Não exige Node, build ou servidor de aplicação.

## Publicar no GitHub Pages

1. Extraia o ZIP.
2. Crie um repositório no GitHub.
3. Envie **o conteúdo da pasta `alphacampus-github`** para a raiz do repositório. O arquivo `index.html` deve ficar na raiz, e não dentro de outra pasta.
4. Abra **Settings → Pages → Build and deployment**.
5. Selecione **Deploy from a branch**, branch **main**, pasta **/(root)** e **Save**.
6. Aguarde a publicação e abra o endereço mostrado pelo GitHub em **Visit site**.

Não envie somente o ZIP: os arquivos precisam estar extraídos no repositório.

Os dados são arquivos estáticos enviados ao navegador. Publicar o site torna `dados.json` acessível aos visitantes, inclusive para download completo. Esta versão não contém autenticação e não inclui CPF ou a planilha original. Revise os registros antes de disponibilizar ao público. Um repositório privado não significa, por si só, um GitHub Pages privado.

## Testar no computador

Dentro da pasta, execute:

```sh
python -m http.server 8000
```

No Windows, você pode usar `py -m http.server 8000`. Abra `http://localhost:8000`. Não abra `index.html` por duplo clique: navegadores bloqueiam a leitura dos JSON por `file://`.

## Arquivos

- `index.html`: página e interface acessível.
- `style.css`: aparência e adaptação ao celular.
- `app.js`: busca, ficha, mapa e links para rotas.
- `config.js`: configuração do Google Maps.
- `dados.json`: registros extraídos da planilha, sem CPF.
- `localizacoes.json`: coordenadas conferidas dos jazigos (inicialmente vazio).
- `logo.png`: logo fornecida.
- `scripts/converter_planilha.py`: atualização dos dados a partir de `.xls`.
- `requirements.txt`: dependência apenas do conversor, não do site.
- `.nojekyll`: publicação estática sem processamento Jekyll.

## Google Maps

O mapa incorporado substitui a imagem estática. O modo padrão usa uma consulta pelo nome e endereço do Alphacampus, em vista satélite, sem chave configurada. Ele depende da disponibilidade do Google Maps e da identificação do endereço pelo Google; confira o local antes de uso público.

Para fixar o mapa do estabelecimento pelo recurso de compartilhamento do Google:

1. Abra o Alphacampus no Google Maps.
2. Clique em **Compartilhar → Incorporar um mapa → Copiar HTML**.
3. Copie somente a URL entre aspas do atributo `src` e coloque em `embedUrl`, dentro de `config.js`.

Outra opção é preencher `googleMapsApiKey` com uma chave da **Maps Embed API** habilitada no seu projeto Google Cloud. Restrinja a chave aos domínios autorizados e à API. A chave tem prioridade sobre `embedUrl`. Não inclua credenciais de servidor no código.

O botão de rota abre o Google Maps; ele permite escolher a origem. Sem coordenadas de jazigo, o destino é **o Alphacampus**, não a sepultura. Nomes de falecidos não são enviados ao Google: apenas a consulta do estabelecimento ou coordenadas cadastradas.

## Localização exata dos jazigos

A planilha tem códigos textuais de quadra, setor e jazigo. Ela não contém latitude/longitude nem a correspondência entre esses códigos e pontos no terreno. Por isso `localizacoes.json` inicia vazio e nenhum ponto é inventado.

Para cadastrar um jazigo, adicione neste arquivo uma propriedade cuja chave seja **exatamente** o campo `location` do registro em `dados.json`. O valor deve ser um objeto com `lat` e `lng` numéricos (coordenadas reais) e `confirmed: true`. O código valida os intervalos e só usa pontos confirmados. Registros com a mesma localização compartilham o ponto.

Ao escolher esse registro, o mapa centraliza no ponto e o link oferece uma rota a pé até ele. **O Google pode não conhecer as vias internas ou acessíveis do cemitério**; o trajeto deve ser conferido pela equipe. Não há navegação interna própria nem rastreamento GPS em tempo real nesta versão.

As antigas marcações em pixels da imagem não são compatíveis com coordenadas GPS. Elas não foram convertidas automaticamente, pois isso exigiria georreferenciar a imagem.

## Atualizar a planilha

Instale a dependência do conversor (Python 3.9+):

```sh
python -m pip install -r requirements.txt
python scripts/converter_planilha.py "Falecidos 27-10-2026.xls" --output dados.json
```

O conversor lê a primeira aba, verifica os cabeçalhos e mantém os registros com nome. Não remove homônimos, pois podem representar pessoas diferentes. Ignora CPF, sexo e outros campos não necessários. Quando falta idade, tenta calcular a idade em anos completos usando nascimento e falecimento válidos. Campo ausente continua ausente; datas inconsistentes não geram idade.

Envie o novo `dados.json` ao GitHub e atualize a data da base em `index.html`. Os dados não sincronizam automaticamente com a planilha.

## Limitações dos dados de origem

Há 17.054 registros com localização e 8.721 com idade. Alguns nomes da base apresentam códigos ou pontuação; foram preservados para evitar exclusões indevidas. A idade é exibida como “não informada” quando ausente. A localização textual vem da coluna “Localização”, sob “Último Evento”; confirme com o cadastro do memorial se representa a localização atual, especialmente em casos de traslado ou exumação.

## Referências

- GitHub Pages: https://docs.github.com/pt/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site
- Incorporar mapas: https://support.google.com/maps/answer/7101463
- Maps Embed API: https://developers.google.com/maps/documentation/embed/embedding-map
- Endereço do Alphacampus: https://www.alphacampus.com.br/contato

O pacote é uma versão independente para o seu repositório. Não contém a identidade de hospedagem do site anterior, histórico Git ou credenciais. Não foi enviado automaticamente para sua conta GitHub.
