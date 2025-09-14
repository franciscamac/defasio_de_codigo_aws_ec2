# Anotações de aprendizados

## Region ou Região
Na AWS, uma região é um local físico espalhado pelo mundo que agrupa datacenters.  
Dentro de cada região, esses datacenters são organizados em grupos chamados **Zonas de Disponibilidade (Availability Zones – AZs)**.  
Cada região conta com pelo menos três AZs, que ficam fisicamente separadas, garantindo maior resiliência e alta disponibilidade.

## Availability Zone
As Zonas de Disponibilidade são datacenters independentes fisicamente, mas interligados por redes de alta largura de banda e baixa latência.  
Isso torna possível operar aplicações e bancos de dados de produção com muito mais **confiabilidade, tolerância a falhas e escalabilidade** do que em um único datacenter.  
Na prática, cada AZ possui energia, rede e conectividade redundantes, e juntas formam a base da infraestrutura de resiliência da AWS.  

📖 Documentação: https://aws.amazon.com/pt/about-aws/global-infrastructure/regions_az/  
![Region](imagem/region.png)

## Serviços
A AWS disponibiliza uma grande variedade de serviços globais na nuvem.  
Entre eles estão soluções de computação, armazenamento, bancos de dados, analytics, redes, machine learning, inteligência artificial, IoT, segurança, ferramentas para desenvolvedores, aplicações empresariais e muito mais.

## EC2
O **Amazon Elastic Compute Cloud (EC2)** é o serviço de computação sob demanda da AWS. Ele permite criar e gerenciar servidores virtuais (as chamadas instâncias) com rapidez e flexibilidade.  
Com isso, é possível reduzir custos de hardware, acelerar implantações e controlar recursos conforme a necessidade.

### Conceitos importantes do EC2
- **Instâncias Virtuais** funcionam como servidores rodando suas aplicações diretamente na nuvem.  
- **AMIs (Amazon Machine Images)** são modelos prontos com sistema operacional e softwares necessários já configurados.  
- **Tipos de instância** oferecem combinações diferentes de CPU, memória, armazenamento e recursos de rede, permitindo escolher o que melhor atende cada cenário.  
- **Amazon EBS** disponibiliza volumes de armazenamento persistentes, ou seja, seus dados são mantidos mesmo que a instância seja desligada.  
- **Armazenamento de instância** é temporário e é excluído quando a instância é parada, hibernada ou encerrada.  

## Serviços que complementam o EC2
Para aproveitar melhor o EC2, a AWS também oferece recursos adicionais, como:  

- **Auto Scaling**, que ajusta automaticamente a quantidade de instâncias conforme a demanda.  
- **AWS Backup**, para automatizar o backup das instâncias e volumes.  
- **Amazon CloudWatch**, que permite monitorar o desempenho da sua infraestrutura.  
- **Elastic Load Balancer**, que distribui o tráfego entre suas instâncias.  
- **Amazon GuardDuty**, que ajuda a detectar usos maliciosos ou não autorizados.  
- **EC2 Image Builder**, que simplifica a criação e atualização de imagens personalizadas de servidores.  
- **AWS Launch Wizard**, que auxilia no provisionamento de recursos sem precisar configurá-los manualmente.  

📖 Documentação: https://aws.amazon.com/pt/ec2/?refid=60b9f03e-f5fe-4f07-846f-f25022f64c72  
![EC2](imagem/ec2.png)

---

# Desafio de Projeto
![Desafio de Projeto](imagem/Desafio_de_projeto_ec2.drawio)

Este projeto foi inspirado em um [estudo de caso publicado no blog da AWS](https://aws.amazon.com/pt/blogs/aws-brasil/alta-performance-e-baixo-custo-numa-solucao-de-saude-com-amazon-cloudfront/).  
O objetivo foi criar uma arquitetura na nuvem que fosse ao mesmo tempo **escalável, resiliente, segura e de baixo custo**, seguindo boas práticas recomendadas pela própria AWS.  

## Como a arquitetura foi pensada

Na camada de **frontend**, o **Amazon CloudFront** ficou responsável por distribuir conteúdo estático e dinâmico com baixa latência para os usuários. Paralelamente, um **bucket S3** foi utilizado para guardar logs e arquivos estáticos.  

Na camada de **aplicação**, utilizei instâncias do **Amazon EC2** dentro de um **Auto Scaling Group**, distribuídas entre as zonas de disponibilidade `sa-east-1a` e `sa-east-1b`. O **Application Load Balancer (ALB)** garante que as requisições sejam divididas entre as instâncias, proporcionando alta disponibilidade.  

Na parte de **banco de dados**, a solução conta com um **PostgreSQL Master**, responsável pelas operações de escrita, e uma **PostgreSQL Read Replica**, que fica encarregada de leituras e replicação assíncrona. Essa separação ajuda a melhorar a performance e a resiliência.  

Também incluí uma camada de **cache** com **Redis Master e Redis Slave**, utilizada tanto para armazenar dados em memória quanto para filas, o que reduziu bastante a carga no banco de dados principal.  

Na parte de **segurança e monitoramento**, usei uma **VPC**, segmentando os recursos em subnets públicas e privadas. O **Internet Gateway** garante a comunicação com a internet, e o **Amazon CloudTrail** registra todas as chamadas de API para auditoria.  

## Principais aprendizados
- **Alta disponibilidade:** utilização de múltiplas zonas de disponibilidade.  
- **Escalabilidade:** Auto Scaling e Load Balancer garantindo ajuste automático conforme o tráfego.  
- **Baixo custo:** CloudFront ajudando a reduzir chamadas diretas ao backend.  
- **Segurança:** recursos sensíveis (como banco de dados e Redis) dentro de subnets privadas.  
- **Observabilidade:** logs coletados pelo CloudTrail e armazenados no S3 para auditoria e análise.