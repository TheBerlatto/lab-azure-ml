# Trabalhando com Machine Learning no Azure ML

Olá a todos! O objetivo desse repositório é descrever o passo a passo sobre a utilização do recurso de aprendizado de máquina automatizado no Aprendizado de Máquina do Azure para treinar e avaliar um modelo de aprendizado de máquina.

Isso faz parte do Desafio de Projeto do curso "Microsoft Azure AI Fundamentals" em parceria com a [DIO](https://dio.me)!

## 👣 Passo a Passo para criar um espaço de trabalho
1. Entre no [Portal do Azure](https://portal.azure.com) e utilize suas credenciais da Microsoft.
2. Selecione **+ Criar um recurso**, procure por **"Azure Machine Learning"** e selecione **"Criar"**.
3. Na tela seguinte, faça as seguintes configurações:
- Assinatura: sua assinatura do Azure.
- Grupo de recursos: crie ou selecione um grupo de recursos.
- Nome: insira um nome exclusivo para seu espaço de trabalho.
- Região: Selecione a região geográfica mais próxima.
- Conta de armazenamento: observe a nova conta de armazenamento padrão que será criada para seu espaço de trabalho.
- Cofre de chaves: observe o novo cofre de chaves padrão que será criado para seu espaço de trabalho.
- Application insights: observe o novo recurso padrão de insights de aplicativo que será criado para seu espaço de trabalho.
- Registro de contêiner: Nenhum (um será criado automaticamente na primeira vez que você implantar um modelo em um contêiner).
4. Selecione **Revisar + criar** e, em seguida, selecione **Criar**. Aguarde até que seu espaço de trabalho seja criado (pode levar alguns minutos) e clique no recurso que acabou de ser implantado.
5. Clique em **Iniciar estúdio** (ou abra uma nova guia do navegador e escreva https://ml.azure.com para entrar no estúdio do Aprendizado de Máquina do Azure usando sua conta da Microsoft). Feche todas as mensagens que forem exibidas.
6. Já dentro do estúdio, você verá seu espaço de trabalho recém-criado. Se não o ver selecione **Todos os espaços de trabalho** no menu à esquerda e, em seguida, selecione o espaço de trabalho que você acabou de criar.

## 👣 Passo a Passo para usar o aprendizado de máquina automatizado para treinar um modelo
1. No [Estúdio de Aprendizado de Máquina do Azure](https://ml.azure.com), exiba a página **ML automatizada** (em Criação).
2. Crie um novo trabalho de ML automatizado com as seguintes configurações, usando **Avançar** conforme necessário para progredir pela interface do usuário:
#### Configurações básicas:
- Nome do trabalho: mslearn-bike-automl
- Novo nome do experimento: mslearn-bike-rental
- Descrição: Aprendizado de máquina automatizado para previsão de aluguel de bicicletas
- Tags: nenhuma
#### Tipo de tarefa & data:
- Selecionar tipo de tarefa: Regressão
- Selecionar conjunto de dados: crie um novo conjunto de dados com as seguintes configurações:
    #### Tipo de dados:
    - Nome: bike-rentals
    - Descrição: Dados históricos de aluguer de bicicletas
    - Tipo: Tabular
- Fonte de dados: Selecionar de arquivos da Web
    #### URL da Web:
    - URL da Web: https://aka.ms/bike-rentals
    - Ignorar validação de dados: não selecione
    #### Configurações:
    - Formato de arquivo: Delimitado
    - Delimitador: Vírgula
    - Codificação: UTF-8
    - Cabeçalhos de coluna: Somente o primeiro arquivo tem cabeçalhos
    - Pular linhas: Nenhum
    - O conjunto de dados contém dados de várias linhas: não selecione
    #### Esquema:
    - Incluir todas as colunas que não sejam **Caminho**
    - Revisar os tipos detectados automaticamente
Selecione **Criar**. Depois que o conjunto de dados for criado, selecione o conjunto de dados de **aluguel de bicicletas** para continuar a enviar o trabalho de ML automatizado.
#### Configurações da Tarefa:
- Tipo de tarefa: Regressão
- Conjunto de dados: aluguel de bicicletas
- Coluna de destino: Aluguéis (inteiro)
- Definições de configuração adicionais:
    - Métrica primária: Erro quadrático médio da raiz normalizada
    - Explicar melhor modelo: Não selecionado
    - Use todos os modelos suportados: Nãoselecionado. Você restringirá o trabalho para tentar apenas alguns algoritmos específicos.
    - Modelos permitidos: selecione apenas RandomForest e LightGBM — normalmente você gostaria de tentar o maior número possível, mas cada modelo adicionado aumenta o tempo necessário para executar o trabalho.
- Limites: expanda esta seção
    - Máximo de tentativas: 3
    - Máximo de tentativas simultâneas: 3
    - Nós máximos: 3
    - Limiar de pontuação métrica: 0,085 (de modo que, se um modelo atingir uma pontuação métrica quadrática média quadrática de raiz normalizada de 0,085 ou menos, o trabalho termina.)
    - Tempo limite: 15
    - Tempo limite de iteração: 15
    - Habilitar rescisão antecipada: Selecionado
- Validação e teste:
    - Tipo de validação: Divisão de validação de trem
    - Porcentagem de dados de validação: 10
    - Conjunto de dados de teste: Nenhum
- Computação:
    - Selecione o tipo de computação: Serverless
    - Tipo de máquina virtual: CPU
    - Camada de máquina virtual: Dedicado
    - Tamanho da máquina virtual: Standard_DS3_V2*
    - Número de instâncias: 1

3. Envie o trabalho de treinamento. Ele começa automaticamente.
4. Aguarde a conclusão do trabalho. Pode demorar uns bons minutos, no meu caso foram 14 minutos.

## 👣 Passo a Passo para implantar e testar o modelo
1. Na guia **Modelo** para obter o melhor modelo treinado pelo seu trabalho de aprendizado de máquina automatizado, selecione **Implantar** e usar a opção **Serviço Web** para implantar o modelo com as seguintes configurações:
- Nome: predict-rentals
- Descrição: Prever aluguéis de ciclos
- Tipo de computação: Instância de Contêiner do Azure
- Habilitar autenticação: Selecionado
2. Aguarde o início da implantação - isso pode levar alguns segundos. O **status de implantação** para o ponto de extremidade de **luguel de previsão** será indicado na parte principal da página como *Em execução*.
3. Aguarde até que o status **Implantar** seja alterado para *Bem-sucedido*. Isso pode levar de 5 a 10 minutos.

## 👣 Passo a Passo para testar o serviço implantado
1. No estúdio do Aprendizado de Máquina do Azure, no menu à esquerda, selecione Pontos de extremidade e abra o ponto de extremidade em tempo real de aluguéis de previsão.
2. Na página de ponto de extremidade em tempo real de aluguéis de previsão, exiba a guia **Teste**.
3. No painel Dados de entrada para testar o ponto de extremidade, substitua o modelo JSON pelos seguintes dados de entrada:
~~~
 {
   "Inputs": { 
     "data": [
       {
         "day": 1,
         "mnth": 1,   
         "year": 2022,
         "season": 2,
         "holiday": 0,
         "weekday": 1,
         "workingday": 1,
         "weathersit": 2, 
         "temp": 0.3, 
         "atemp": 0.3,
         "hum": 0.3,
         "windspeed": 0.3 
       }
     ]    
   },   
   "GlobalParameters": 1.0
 } 
 ~~~
4. Clique em **Testar**.



