# game_reviews_etl_with_azure

Olá, tudo bem contigo? 😃

Inicialmente, o projeto simula a integração de um ambiente on-premises com a Azure, onde as análises de jogos da API da Steam são extraídas e armazenadas em formato bruto em um banco de dados MongoDB.

Após o armazenamento das análises, elas são migradas para o ambiente Azure por meio de uma pipeline criada com o Azure Data Factory.

O projeto pode ser modificado, tendo em vista que tenho o objetivo de utilizar os dados migrados para análise de dados e Machine Learning.

## Etapas do projeto

### Extrair as análises da API da Steam

Utilizando o [notebook](extrat_reviews.ipynb), as análises são armazenadas no banco de dados MongoDB.

Para a execução do notebook, é necessário um arquivo `.env`, como este [exemplo](.env.example), contendo as seguintes informações:

- URI do seu banco MongoDB
- Database e collection que serão utilizadas para armazenar as análises
- ID do jogo na Steam

Também é necessário instalar as [dependências](requirements.txt) do notebook. Utilize o seguinte trecho de código para instalá-las:

```bash
pip install -r requirements.txt
```

### Migração de dados on-premises para o ambiente Azure

Para a migração dos dados, são necessários alguns recursos. Eles são:

- Azure Data Factory para a criação da pipeline
- Uma instância de Integration Runtime self-hosted
- Uma instância MongoDB atrelada à sua instância de Integration Runtime self-hosted
- Uma conta de armazenamento
- Blob Storage para a conta de armazenamento

A seguir, estão os passos para a criação da pipeline.

#### Criação da instância de Integration Runtime

1. Acesse `Gerenciar > Runtimes de integração` e clique em `+ Novo`.

![image](https://github.com/user-attachments/assets/419cff91-4a6a-4da0-8a40-0da6efb0c2df)

2. Selecione a opção **Azure, Auto-Hospedado**.

![image](https://github.com/user-attachments/assets/44b9016b-01bb-4fb8-accd-14ce407ee6ac)

3. Selecione a opção **Auto-Hospedado**.

![image](https://github.com/user-attachments/assets/b3c28a33-c365-46a4-a3e4-cff44d693d5c)

4. Após informar o nome da instância, baixe o executável disponibilizado.

![image](https://github.com/user-attachments/assets/928c4793-e8ba-4b14-ace4-6f1a873aa12c)

5. Execute a aplicação baixada.

![Captura de tela 2025-04-04 191837](https://github.com/user-attachments/assets/65446509-57ab-458d-8c36-b630f32661fd)

6. Após a execução, a instância deve aparecer com um ícone verde de confirmação.

![image](https://github.com/user-attachments/assets/97832fb6-23c8-46ff-ad89-23129c49edb8)

Pronto! Sua instância de Integration Runtime self-hosted foi criada e agora você pode se conectar ao banco de dados MongoDB local.

#### Conexão com o MongoDB

1. Ainda na aba **Gerenciar**, acesse `Serviços Vinculados` e clique em `+ Novo`.

![Captura de tela 2025-04-04 192425](https://github.com/user-attachments/assets/4aa53349-a619-4111-b21c-7de93574203e)

2. Procure por MongoDB e selecione o serviço MongoDB.

![Captura de tela 2025-04-04 192514](https://github.com/user-attachments/assets/1980d6ff-e194-4865-a560-bbec9ea28f89)

3. Preencha o nome do serviço a ser vinculado e selecione a instância de Integration Runtime self-hosted criada.

![image](https://github.com/user-attachments/assets/a106e458-8dfe-488a-9a82-9b3367a76465)

4. Teste a conexão do serviço e verifique se ele está presente na lista de `Serviços Vinculados`.

![image](https://github.com/user-attachments/assets/11674a01-e632-4000-90b3-f8ffe1992dce)

#### Criação do Blob Storage para armazenamento dos arquivos

1. Na página de `Serviços Vinculados`, clique novamente em `+ Novo`, busque por Blob e selecione o serviço `Armazenamento de Blob do Azure`.

![image](https://github.com/user-attachments/assets/b9ae311c-d8d5-47c3-be4a-9427f4dbe1e1)

2. Preencha as informações com o nome do serviço, selecione a instância de Integration Runtime do Azure — cuidado para não selecionar a instância self-hosted —, sua assinatura e a conta de armazenamento.

![image](https://github.com/user-attachments/assets/ce2e8b6f-0a9e-4131-928c-c56c811e1422)

3. Teste a conexão e verifique se o serviço está presente na lista de `Serviços Vinculados`.

![image](https://github.com/user-attachments/assets/32b283ae-dd96-4dc9-82af-03d27357f551)

#### Criação da pipeline de dados

1. Acesse `Página Inicial > Novo > Pipeline`.

![Captura de tela 2025-04-04 193552](https://github.com/user-attachments/assets/938c21f0-412c-4238-9a40-dd2b08bcde93)

2. Crie uma nova atividade de **Copiar Dados**.

![Captura de tela 2025-04-04 194052](https://github.com/user-attachments/assets/a93235de-940d-433e-80b6-4401e8912e96)

3. Clique na atividade no espaço de fluxos, acesse a aba **Origem** e selecione o serviço MongoDB criado.

![image](https://github.com/user-attachments/assets/9e6dfe9d-6916-4cee-a7c1-5d080df55b2f)

4. Após definir a origem, clique na aba **Coletor** e selecione o serviço Blob Storage criado.

![image](https://github.com/user-attachments/assets/12b5f2a5-0d9f-4c39-8578-c28ff0ea8308)

5. Valide a pipeline de dados e faça a depuração. Se tudo for executado corretamente, você verá seu arquivo no contêiner criado para os arquivos e poderá baixá-lo.

![Captura de tela 2025-04-04 204009](https://github.com/user-attachments/assets/b8bc48e7-f771-49c8-82e5-ff369d261763)

![image](https://github.com/user-attachments/assets/ca02f14e-0ddb-4817-b39a-869129d3fb30)

**Voilà!** As análises que antes estavam no ambiente on-premises agora estão no ambiente Azure, prontas para serem utilizadas.

Se você chegou até aqui, sinta-se à vontade para me enviar seu feedback, seja ele positivo 👍 ou negativo 👎. Até mais 😉
