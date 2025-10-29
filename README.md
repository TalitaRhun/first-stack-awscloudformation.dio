# first-stack-awscloudformation.dio
Anotações e Insights sobre a AWS CloudFormation

AWS CloudFormation é um processo que auxilia na automação de criação de recursos na AWS por meio de templates Json e YAML.
Além de ser um processo automatizado, é possivel versionar os templates e criar desde recursos simples como o EC2 até uma arquitetura com vários recursos.

🎯 Objetivo!
Criar um template AWS CloudFormation simples para um S3 Bucket ou um Security Group.

* Fazer o upload e criar a Stack no console da AWS.
* Entender o ciclo de vida da Stack.
* Atualizar a Stack (UPDATE).
* Deletar a Stack (DELETE).

  1️⃣ Configuração Inicial e Escolha do Recurso
Passos:
1. Acesso à Conta AWS: Garanta que você tem as credenciais de acesso e permissões necessárias (IAM) para criar recursos e usar o CloudFormation.
2. Escolha do Recurso Simples: Decida qual recurso AWS você irá provisionar.
3. Sugestão para iniciantes: Um AWS S3 Bucket. É um recurso simples e fácil de verificar.

NOTE: 
- Prática de Least Privilege: Se estiver usando um usuário IAM, documente quais permissões mínimas ele precisa (ex: cloudformation:*, s3:*).
- Região da AWS: Anote qual região você está usando (ex: us-east-1). A CloudFormation Stack e os recursos são regionais.

  2️⃣ Criação do Template CloudFormation
O template é o coração do IaC. Ele pode ser escrito em YAML ou JSON. (O YAML é geralmente preferido por ser mais legível)

Passos:
1. Crie um arquivo de template (ex: s3-bucket-template.yaml).

2. Defina a estrutura mínima:
* AWSTemplateFormatVersion: Versão do formato do template (sempre 2010-09-09).
* Resources: A seção onde você define os recursos AWS.

  // Exemplo de Template (YAML):

    AWSTemplateFormatVersion: '2010-09-09'
Description: Meu primeiro Stack CloudFormation - S3 Bucket

Resources:
  MySimpleS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: meu-primeiro-cf-bucket-abc-123  # DEVE ser globalmente único!
      AccessControl: Private
      Tags:
        - Key: Environment
          Value: Dev
        - Key: Project
          Value: CloudFormationLab
NOTE:
- Sintaxe do Template:	Destaque que o Type segue o formato AWS::Serviço::Recurso (ex: AWS::S3::Bucket).
- Propriedades (Properties):	Mencione que as propriedades são específicas do recurso e correspondem aos campos de configuração do console AWS/API.
  ** Insights:
- YAML vs. JSON: O YAML é mais legível e suporta comentários, mas o JSON é o formato original. Ambos são aceitos.
- Validação: Use a ferramenta cfn-lint (se estiver no seu ambiente local) ou o validador do Console da AWS para checar erros de sintaxe antes de subir a Stack.

  3️⃣ Criação da Stack
Esta é a fase de provisionamento do recurso.

Passos:
1. Acesse o console da AWS CloudFormation.
2. Clique em "Create stack" (Criar pilha).
3. Preparar Template: Selecione "Template is ready" (Modelo está pronto).
4. Especificar Template: Faça o upload do seu arquivo s3-bucket-template.yaml.
5. Especificar Detalhes da Stack:
- Stack name (Nome da pilha): Dê um nome significativo (ex: S3-Lab-Stack).
- Parameters (Parâmetros): (Se houver, pule por enquanto, pois o template é simples).
6. Configurar Opções da Stack: Mantenha as opções padrão por enquanto.
7. Revisar e Criar: Revise e clique em "Create stack".

NOTE: 
-Ciclo de Vida: Documente o status da Stack na aba Events (Eventos): CREATE_IN_PROGRESS $\rightarrow$ CREATE_COMPLETE.
-Rollback:	Insight: Se a criação falhar (ex: nome do bucket já existe), a CloudFormation fará o Rollback e tentará excluir os recursos que criou, definindo o status para ROLLBACK_COMPLETE.
-Recursos Criados:	Verifique a aba Resources (Recursos) para confirmar que o S3 Bucket foi criado e anote o ID físico (nome real do bucket).

4️⃣ Atualização da Stack
O IaC permite que você faça modificações de forma controlada.

Passos:
1. Modifique o template: Adicione, por exemplo, um novo Tag ao seu S3 Bucket no arquivo s3-bucket-template.yaml.
2. Inicie a atualização no console: Selecione a Stack e clique em "Update".
3. Faça o upload do novo template modificado.
4. Review Changes: (Opcional, mas recomendado) Veja o Change Set (Conjunto de Mudanças) gerado. Ele mostra exatamente o que a CloudFormation irá mudar.
5. Execute a atualização.
          
NOTE:
-Change Sets: O Change Set é crucial! Ele atua como uma "pré-visualização" de todas as alterações, permitindo validar o impacto antes da execução.
-Status de Atualização: Documente o status: UPDATE_IN_PROGRESS $\rightarrow$ UPDATE_COMPLETE.
-Tipos de Atualização: Insight: Algumas mudanças (ex: renomear um bucket S3) podem exigir que a CloudFormation Substitua o recurso, ou seja, exclua o antigo e crie um novo (com potencial interrupção). Outras são apenas modificações.

5️⃣ Remoção da Stack
A grande vantagem do IaC é a capacidade de limpar o ambiente de forma completa e segura.

Passos:
1. Selecione a Stack no console da CloudFormation.
2. Clique em "Delete" (Excluir).
3. Confirme a exclusão.

NOTE:
-Remoção Limpa:	Insight: A CloudFormation exclui todos os recursos que ela criou. Isso previne o "drift" (desvio) e os custos de recursos esquecidos.
-Status de Deleção: Documente o status: DELETE_IN_PROGRESS $\rightarrow$ DELETE_COMPLETE. Após isso, a Stack desaparece.
-Recursos com Conteúdo:	Cuidado: Se o S3 Bucket tivesse arquivos, a deleção da Stack falharia, a menos que você adicione uma política específica (DeletionPolicy: Retain ou configure o esvaziamento do bucket).
