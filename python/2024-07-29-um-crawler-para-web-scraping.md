---
title: "Como Fazer Web Scraping com BeautifulSoup e Pandas"
tags: web scraping, BeautifulSoup, Pandas, Python, extração de dados, crawler, HTML, análise de dados, tutorial, automação, datasheets, SMD, código de exemplo, CSV, parsing de HTML
subheadline: web scraping com BeautifulSoup e Pandas
teaser: Aprenda a realizar web scraping com BeautifulSoup e Pandas para extrair dados de websites de forma eficiente. Este tutorial prático inclui exemplos de código e explica cada passo detalhadamente.
categories: [programacao,python]
layout: article
share: true
toc: true
comments: true
coinbase:
  show: false
google:
  plusone: true
ads:
 show: true
tagcloud: true
---

O web scraping é uma técnica para extrair dados de websites. Comumente, utiliza-se bibliotecas como BeautifulSoup e Pandas para realizar essa tarefa. Este artigo abordará como utilizar essas ferramentas para criar um crawler que obtém dados não facilmente acessíveis pela interface do usuário.

<!--more-->

### Bibliotecas Utilizadas

#### BeautifulSoup
O BeautifulSoup é uma biblioteca Python para parsing de arquivos HTML e XML. Ele cria uma árvore de análise a partir das páginas carregadas, que pode ser usada para extrair dados de HTML de forma prática.

#### Pandas
O Pandas é uma biblioteca de análise de dados poderosa e fácil de usar. Ele proporciona estruturas de dados rápidas, flexíveis e expressivas, projetadas para facilitar a manipulação e análise de dados.

### Agradecimento Especial

Gostaria de fazer um agradecimento especial a Roberto Teixeira (Beto Byte) por todo o suporte e ensinamentos durante o curso de Pandas, disponível em [Curso de Pandas da CIEDA](https://cieda.com.br/curso-pandas). O conhecimento adquirido nesse curso foi essencial para o desenvolvimento deste projeto.

### Exemplo Prático: Crawler de SMD Datasheets

A seguir, apresentamos um exemplo de um script Python que utiliza BeautifulSoup e Pandas para realizar web scraping em uma página de SMD datasheets, baixando arquivos e processando tabelas.

#### Estrutura do Script

O script fornecido é dividido em várias funções que realizam tarefas específicas:

- **download_file**: Baixa um arquivo da internet.
- **process_smd_table**: Processa a tabela principal de SMD datasheets e faz o parsing das linhas da tabela.
- **process_sort_table**: Processa tabelas de dados individuais de cada SMD.
- **load_packages_csv** e **save_packages_csv**: Geram e salvam arquivos CSV com as imagens de encapsulamento dos componentes, facilitando futuras expansões e análises.
- **load_manufacturers_csv** e **save_manufacturers_csv**: Geram e salvam arquivos CSV com informações dos fabricantes, que podem ser usadas para criar páginas descritivas de cada empresa.
- **update_template**: Atualiza um template HTML com dados processados.
- **save_all_sort_data**: Salva todos os dados processados em um arquivo HTML.

A seguir, explicamos cada uma das partes principais do código.

#### Função `download_file`

Esta função é responsável por baixar arquivos da internet, seja uma imagem ou um PDF. Caso o arquivo já exista no diretório local, ele não será baixado novamente.

```python
def download_file(url, local_path):
    print(f"Tentando baixar: {url} para {local_path}")
    if os.path.exists(local_path):
        print(f"Arquivo já existe: {local_path}")
        return
    
    try:
        response = requests.get(url, stream=True)
        response.raise_for_status()
        os.makedirs(os.path.dirname(local_path), exist_ok=True)
        with open(local_path, 'wb') as file:
            for chunk in response.iter_content(1024):
                file.write(chunk)
        print(f"Arquivo baixado: {url} -> {local_path}")
    except requests.exceptions.RequestException as e:
        print(f"Erro ao baixar o arquivo {url}: {e}")
```

#### Função `process_smd_table`

Esta função acessa a página principal dos SMD datasheets, encontra a tabela de dados e inicia o processamento de cada linha da tabela.

```python
def process_smd_table(output_dir):
    base_url = "https://www.s-manuals.com/smd"
    try:
        response = requests.get(base_url)
        response.raise_for_status()
    except requests.exceptions.RequestException as e:
        print(f"Erro ao acessar a URL {base_url}: {e}")
        return

    soup = BeautifulSoup(response.content, 'html.parser')
    table = soup.find('table', id='smdTable')
    if not table:
        print("Tabela com id='smdTable' não encontrada.")
        return

    rows = table.find_all('tr')
    print(f"Número de linhas na tabela: {len(rows)}")

    manufacturers = load_manufacturers_csv(MANUFACTURERS_CSV)
    all_sort_data = []
    all_package = load_packages_csv(PACKAGES_CSV)

    for row in rows[1:]:
        cells = row.find_all('th')
        for cell in cells:
            link_tag = cell.find('a')
            if link_tag and 'href' in link_tag.attrs:
                link = link_tag['href']
                file_name = link.split('/')[-1] + '.html'
                print(f"Processando link: {link}")
                process_sort_table(link, os.path.join(output_dir, file_name), manufacturers, all_sort_data, all_package)
                link_tag['href'] = file_name
            else:
                cell = ''
                
        cells = row.find_all('td')
        for cell in cells:
            link_tag = cell.find('a')
            if link_tag and 'href' in link_tag.attrs:
                link = link_tag['href']
                file_name = link.split('/')[-1] + '.html'
                print(f"Processando link: {link}")
                process_sort_table(link, os.path.join(output_dir, file_name), manufacturers, all_sort_data, all_package)
                link_tag['href'] = file_name
            else:
                cell = ''

    update_template(str(table), os.path.join(output_dir, "index.html"), "SMD Datasheets")
    if not os.path.exists(MANUFACTURERS_CSV):
        save_manufacturers_csv(manufacturers, MANUFACTURERS_CSV)
    save_all_sort_data(all_sort_data, os.path.join(output_dir, "todos.html"))
```

#### Função `process_sort_table`

Esta função acessa as páginas de detalhes de cada SMD, processa a tabela de dados, e baixa arquivos adicionais (como imagens e PDFs) relacionados aos SMDs.

```python
def process_sort_table(url, local_file, manufacturers, all_sort_data, all_package):
    print(f"Acessando URL: {url}")
    try:
        response = requests.get(url)
        response.raise_for_status()
    except requests.exceptions.RequestException as e:
        print(f"Erro ao acessar a URL {url}: {e}")
        return

    soup = BeautifulSoup(response.content, 'html.parser')
    table = soup.find('table', id='sortTable')
    if not table:
        print("Tabela com id='sortTable' não encontrada.")
        return

    headers = [header.text.strip() for header in table.find_all('th')]
    for row in table.find_all('tr')[1:]:
        cells = row.find_all('td')
        row_data = dict(zip(headers, cells))

        if 'Manufacturer' in row_data:
            manufacturer = row_data['Manufacturer'].text.strip()
            manufacturers.add(manufacturer)

        package_img_src_local = ''

        if 'SMD Code' in row_data:
            img_tag = row_data['SMD Code'].find('img')
            if img_tag:
                package_img = img_tag['src']
                if package_img.startswith("//"):
                    package_img = "https:" + package_img
                elif package_img.startswith("/"):
                    package_img = "https://www.s-manuals.com" + package_img
                elif package_img.startswith("img"):
                    package_img = "https://www.s-manuals.com/" + package_img
                package_img_src_local = os.path.join(IMG_DIR, os.path.basename(package_img))
                package_img_local = os.path.join(os.path.dirname(local_file), package_img_src_local)
                print(f"Baixando imagem do pacote: {package_img} para {package_img_local}")
                download_file(package_img, package_img_local)
                img_tag['src'] = package_img_src_local
            all_sort_data.append(row_data)
        
        if 'Package' in row_data:
            img_tag = row_data['Package'].find('img')
            if img_tag:
                package_img = img_tag['src']
                if package_img.startswith("//"):
                    package_img = "https:" + package_img
                elif package_img.startswith("/"):
                    package_img = "https://www.s-manuals.com" + package_img
                elif package_img.startswith("img"):
                    package_img = "https://www.s-manuals.com/" + package_img
                package_img_src_local = os.path.join(IMG_DIR, os.path.basename(package_img))
                package_img_local = os.path.join(os.path.dirname(local_file), package_img_src_local)
                print(f"Baixando imagem do pacote: {package_img} para {package_img_local}")
                download_file(package_img, package_img_local)
                img_tag['src'] = package_img_src_local
            all_package.add(package_img_src_local)
            
        if 'Datasheet' in row_data:
            a_tag = row_data['Datasheet'].find('a')
            if a_tag:
                datasheet_pdf = a_tag['href']
                if datasheet_pdf.startswith("//"):
                    datasheet_pdf = "http:" + datasheet_pdf
                datasheet_pdf_href_local = os.path.join("pdfs", os.path.basename(datasheet_pdf))
                datasheet_pdf_local = os.path.join(os.path.dirname(local_file), datasheet_pdf_href_local)
                print(f"Baixando PDF da ficha técnica: {datasheet_pdf}")
                download_file(datasheet_pdf, datasheet_pdf_local)
                a_tag['href'] = datasheet_pdf_href_local
                img_tag = a_tag.find('img')
                if img_tag:
                    datasheet_pdf_img_src = img_tag['src']
                    if datasheet_pdf_img_src.startswith("//"):
                        datasheet_pdf_img_src = "https:" + datasheet_pdf_img_src
                    elif datasheet_pdf_img_src.startswith("/"):
                        datasheet_pdf_img_src = "https://www.s-manuals.com" + datasheet_pdf_img_src
                    elif datasheet_pdf_img_src.startswith("img"):
                        datasheet_pdf_img_src = "https://www.s-manuals.com/" + datasheet_pdf_img_src
                    datasheet_pdf_img_src_local = os.path.join(IMG_DIR, os.path.basename(datasheet_pdf_img_src))
                    datasheet_pdf_img_local = os.path.join(os.path.dirname(local_file), datasheet_pdf_img_src_local)
                    print(f"Baixando imagem do pacote: {datasheet_pdf_img_src} para {datasheet_pdf_img_local}")
                    download_file(datasheet_pdf_img_src, datasheet_pdf_img_local)
                    img_tag['src'] = datasheet_pdf_img_src_local

    back_link = '<p><a href="index.html">Voltar para a página principal</a></p>'
    table_with_back_link = str(table) + back_link
    smd_code = url.split('/')[-1]
    page_title = f"SMD Datasheets - {smd_code}"
    update_template(table_with_back_link, local_file, page_title)
```

#### Função `load_packages_csv` e `save_packages_csv`

Essas funções geram um arquivo CSV com as imagens de encapsulamento dos componentes, possibilitando análise e expansão futura com outro crawler ou inserção direta em um banco de dados.

```python
def load_packages_csv(csv_file):
    packages = set()
    if os.path.exists(csv_file):
        with open(csv_file, 'r', newline='') as file:
            reader = csv.reader(file)
            next(reader)  # Skip header row
            for row in reader:
                if row:
                    package = row[0]
                    description = row[1]
                    image = row[2]
                    packages.add((package, description, image))
    print(f"Carregado packages do CSV: {packages}")
    return packages

def save_package_csv(packages, csv_file):
    with open(csv_file, 'w', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(["ID", "Package", "Description", "Image"])
        id = 1
        for package, description, image in packages:
            writer.writerow([id, package, description, image])
            id += 1
            
    print(f"Arquivo CSV salvo em: {csv_file}")
```

#### Função `load_manufacturers_csv` e `save_manufacturers_csv`

Essas funções foram criadas para carregar e salvar informações sobre os fabricantes em arquivos CSV. Esses dados podem ser usados para gerar páginas descritivas de cada empresa.

```python
def load_manufacturers_csv(csv_file):
    manufacturers = set()
    if os.path.exists(csv_file):
        with open(csv_file, 'r', newline='') as file:
            reader = csv.reader(file)
            next(reader)  # Skip header row
            for row in reader:
                if row:
                    manufacturer = row[0]
                    manufacturers.add(manufacturer)
    print(f"Carregado fabricantes do CSV: {manufacturers}")
    return manufacturers

def save_manufacturers_csv(manufacturers, csv_file):
    with open(csv_file, 'w', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(["Manufacturers", "Site"])
        for manufacturer in manufacturers:
            writer.writerow([manufacturer])
    print(f"Arquivo CSV salvo em: {csv_file}")
```

### Conclusão

Com essas ferramentas, você pode automatizar a extração de dados da web, organizar as informações em formatos estruturados e utilizá-las conforme necessário. Utilizando BeautifulSoup e Pandas, conseguimos criar um crawler eficaz para obter datasheets de componentes SMD. Essa técnica pode ser aplicada em diversos outros cenários onde há a necessidade de extração de dados web.
