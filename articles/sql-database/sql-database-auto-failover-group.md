---
title: Grupos de failover
description: Os grupos de failover automático é um recurso do Banco de Dados SQL que permite que você gerencie a replicação e failover automático/coordenado de um grupo de bancos de dados em um servidor do Banco de Dados SQL ou todos os bancos de dados na instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 1/05/2020
ms.openlocfilehash: 7b45ddce0435a903c63855dea8a01353a7ab36ec
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722536"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Use grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados

Os grupos de failover automático são um recurso de banco de dados SQL que permite que você gerencie a replicação e o failover de um grupo de bancos de dados em um servidor de banco de dados SQL ou de todos os bancos em uma instância gerenciada para outra região. É uma abstração declarativa sobre o recurso de [replicação geográfica ativa](sql-database-active-geo-replication.md) existente, projetado para simplificar a implantação e o gerenciamento de bancos de dados replicados geograficamente em escala. Você pode iniciar o failover manualmente ou pode delegá-lo para o serviço de Banco de Dados SQL com base em uma política definida pelo usuário. A última opção permite que você recupere automaticamente vários bancos de dados relacionados em uma região secundária após uma falha catastrófica ou outro evento não planejado que resulte em perda total ou parcial de disponibilidade do serviço de Banco de Dados SQL na região primária. Um grupo de failover pode incluir um ou vários bancos de dados, normalmente usados pelo mesmo aplicativo. Além disso, eles podem usar os bancos de dados secundários legíveis para descarregar cargas de trabalho de consulta somente leitura. Como os grupos de failover automático incluem vários bancos de dados, esses bancos de dados devem ser configurados no servidor primário. Os grupos de failover automático oferecem suporte à replicação de todos os bancos de dados no grupo para apenas um servidor secundário em uma região diferente.

> [!NOTE]
> Ao trabalhar com bancos de dados individuais ou em pool em um servidor do Banco de Dados SQL, se quiser vários secundários nas mesmas regiões ou em regiões diferentes, use a [replicação geográfica ativa](sql-database-active-geo-replication.md). 

Ao usar grupos de failover automático com uma política de failover automático, qualquer interrupção que afete um ou vários bancos de dados no grupo resultar em failover automático. Normalmente, esses são incidentes que não podem ser autoatenuados pelas operações de alta disponibilidade automáticas internas. Os exemplos de gatilhos de failover incluem um incidente causado por um anel de locatário ou anel de controle do SQL que está sendo inativo devido a um vazamento de memória do kernel do sistema operacional em vários nós de computação ou a um incidente causado por um ou mais anéis de locatário sendo desativados porque um cabo de rede incorreto foi cortado durante ro descomissionamento de hardware utine.  Para obter mais informações, consulte [alta disponibilidade do banco de dados SQL](sql-database-high-availability.md).

Além disso, os grupos de failover automático fornecem pontos de extremidade de ouvinte de leitura/gravação e somente leitura que permanecem inalterados durante failovers. Não importa se você usa a ativação de failover manual ou automática, o failover alterna todos os bancos de dados secundários no grupo para primário. Após o failover de banco de dados ser concluído, o registro DNS é atualizado automaticamente para redirecionar os pontos de extremidade para a nova região. Para os dados específicos de RPO e RTO, confira [Visão geral da continuidade de negócios](sql-database-business-continuity.md).

Ao usar grupos de failover automático com uma política de failover automático, qualquer interrupção que afete bancos de dados no servidor do Banco de Dados SQL ou na instância gerenciada resulta em um failover automático. Você pode gerenciar o grupo de failover automático usando:

- [Azure portal](sql-database-implement-geo-distributed-database.md)
- [CLI do Azure: grupo de failover](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST: grupo de failover](/rest/api/sql/failovergroups).

Após o failover, verifique se os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo primário. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Para garantir a continuidade de negócios real, a adição de redundância de banco de dados entre datacenters é apenas parte da solução. A recuperação de um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. O software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas necessárias para garantir que seu serviço funcione durante o failover dos serviços dos quais ele depende. Para obter mais informações sobre como criar soluções para a recuperação de desastre, veja [Criando soluções de nuvem para a recuperação de desastre usando a replicação geográfica ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Funcionalidades e terminologia de grupo de failover automático

- **Grupo de failover (neblina)**

  Um grupo de failover é um grupo nomeado de bancos de dados gerenciados por um único servidor de banco de dados SQL ou em uma única instância gerenciada que pode fazer failover como uma unidade para outra região caso todos ou alguns bancos de dados primários fiquem indisponíveis devido a uma interrupção na região primária. Quando criadas para instâncias gerenciadas, um grupo de failover contém todos os bancos de dados de usuário na instância e, portanto, apenas um grupo de failover pode ser configurado em uma instância do.
  
  > [!IMPORTANT]
  > O nome do grupo de failover deve ser globalmente exclusivo dentro do domínio `.database.windows.net`.

- **Servidores do Banco de Dados SQL**

     Com servidores do Banco de Dados SQL, alguns ou todos os bancos de dados do usuário em um único servidor podem ser colocados em um grupo de failover. Além disso, um servidor do Banco de Dados SQL dá suporte a vários grupos de failover em um único servidor do Banco de Dados SQL.

- **Primário**

  O servidor de banco de dados SQL ou a instância gerenciada que hospeda os bancos dos dados primários no grupo de failover.

- **Secundário**

  O servidor de banco de dados SQL ou a instância gerenciada que hospeda os bancos dos dados secundários no grupo de failover. O secundário não pode estar na mesma região do primário.

- **Adicionar bancos de dados individuais ao grupo de failover**

  É possível colocar vários bancos de dados individuais no mesmo servidor do Banco de Dados SQL no mesmo grupo de failover. Se você adicionar um banco de dados individual ao grupo de failover, ele criará automaticamente um banco de dados secundário usando a mesma edição e tamanho da computação no servidor secundário.  Você especificou esse servidor ao criar o grupo de failover. Se você adicionar um banco de dados que já possui um banco de dados secundário no servidor secundário, esse vínculo de replicação geográfica é herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no servidor secundário.

  > [!IMPORTANT]
  > Certifique-se de que o servidor secundário não tenha um banco de dados com o mesmo nome, a menos que seja um banco de dados secundário existente. Em grupos de failover para instância gerenciada, todos os bancos de dados de usuário são replicados. Você não pode escolher um subconjunto de bancos de dados de usuário para replicação no grupo de failover.

- **Adicionar bancos de dados no pool elástico para o grupo de failover**

  É possível colocar todos ou vários bancos de dados dentro de um pool elástico no mesmo grupo de failover. Se o banco de dados primário estiver em um pool elástico, o banco de dados secundário é criado automaticamente no pool elástico com o mesmo nome (pool secundário). Você deve garantir que o servidor secundário contém um pool elástico com exatamente o mesmo nome e capacidade livre suficiente para hospedar os bancos de dados secundários que serão criados pelo grupo de failover. Se você adicionar um banco de dados no pool que já possui um banco de dados secundário no pool secundário, esse vínculo de replicação geográfica é herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no pool secundário.
  
- **Propagação inicial** 

  Ao adicionar bancos de dados, pools elásticos ou instâncias gerenciadas a um grupo de failover, há uma fase de propagação inicial antes do início da replicação de dados. A fase de propagação inicial é a operação mais longa e cara. Após a conclusão da propagação inicial, os dados são sincronizados e, em seguida, somente as alterações de dados subsequentes são replicadas. O tempo necessário para que a semente inicial seja concluída depende do tamanho dos dados, do número de bancos de dado replicados e da velocidade do link entre as entidades no grupo de failover. Em circunstâncias normais, a velocidade de propagação típica é de 50-500 GB a uma hora para um único banco de dados ou pool elástico, e 18-35 GB de hora para uma instância gerenciada. A propagação é executada para todos os bancos de dados em paralelo. Você pode usar a velocidade de propagação declarada, juntamente com o número de bancos de dados e o tamanho total do dado para estimar quanto tempo a fase de propagação inicial levará antes de iniciar a replicação de dados.

  Para instâncias gerenciadas, a velocidade do link de rota expressa entre as duas instâncias também precisa ser considerada ao estimar o tempo da fase de propagação inicial. Se a velocidade do link entre as duas instâncias for mais lenta do que o necessário, é provável que o tempo de propagação seja notavelmente afetado. Você pode usar a velocidade de propagação declarada, o número de bancos de dados, o tamanho total e a velocidade do link para estimar quanto tempo a fase de propagação inicial levará antes de iniciar a replicação de dados. Por exemplo, para um único banco de dados de 100 GB, a fase de semente inicial levaria de 2,8 a 5,5 horas se o link for capaz de enviar por push 35 GB por hora. Se o link só puder transferir 10 GB por hora, a propagação de um banco de dados de 100 GB levará cerca de 10 horas. Se houver vários bancos de dados a serem replicados, a propagação será executada em paralelo e, quando combinada com uma velocidade de link lento, a fase de propagação inicial poderá levar muito mais tempo, especialmente se a propagação paralela de dados de todos os bancos de dado exceder o disponível largura de banda do link. Se a largura de banda de rede entre duas instâncias for limitada e você estiver adicionando várias instâncias gerenciadas a um grupo de failover, considere adicionar várias instâncias gerenciadas ao grupo de failover sequencialmente, uma a uma.

  
- **Zona DNS**

  Uma ID exclusiva que é gerada automaticamente quando uma nova instância é criada. Um certificado de vários domínios (SAN) para essa instância é provisionado para autenticar as conexões de cliente com qualquer instância na mesma zona DNS. As duas instâncias gerenciadas no mesmo grupo de failover devem compartilhar a zona DNS.
  
  > [!NOTE]
  > Uma ID de zona DNS não é necessária para grupos de failover criados para servidores de banco de dados SQL.

- **Ouvinte de leitura/gravação do grupo de failover**

  Um registro DNS CNAME que aponta para a URL primária atual. Ele é criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho de leitura/gravação do SQL Reconecte-se de forma transparente ao banco de dados primário quando o primário é alterado após o failover. Quando o grupo de failover é criado em um servidor de banco de dados SQL, o registro DNS CNAME da URL do ouvinte é formado como `<fog-name>.database.windows.net`. Quando o grupo de failover é criado em uma instância gerenciada, o registro DNS CNAME para a URL do ouvinte é formado como `<fog-name>.zone_id.database.windows.net`.

- **Ouvinte de somente leitura do grupo de failover**

  Foi formado um registro CNAME de DNS que aponta ao ouvinte somente leitura que aponta à URL do secundário. Ele é criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho SQL somente leitura se conecte de forma transparente ao secundário usando as regras de balanceamento de carga especificadas. Quando o grupo de failover é criado em um servidor de banco de dados SQL, o registro DNS CNAME da URL do ouvinte é formado como `<fog-name>.secondary.database.windows.net`. Quando o grupo de failover é criado em uma instância gerenciada, o registro DNS CNAME para a URL do ouvinte é formado como `<fog-name>.zone_id.secondary.database.windows.net`.

- **Política de failover automático**

  Por padrão, um grupo de failover é configurado com uma política de failover automático. O serviço de Banco de Dados SQL dispara o failover após a falha ser detectada e o período de cortesia expirar. O sistema deve verificar se a interrupção não pode ser mitigada pela [infraestrutura de alta disponibilidade interna do serviço do Banco de Dados SQL](sql-database-high-availability.md) devido à escala do impacto. Se você deseja controlar o fluxo de trabalho de failover do aplicativo, pode desligar o failover automático.
  
  > [!NOTE]
  > Como a verificação da escala da interrupção e a rapidez com que ela pode ser atenuada envolve ações humanas pela equipe de operações, o período de carência não pode ser definido abaixo de uma hora.  Essa limitação se aplica a todos os bancos de dados no grupo de failover, independentemente de seu estado de sincronização de dados. 

- **Política de failover somente leitura**

  Por padrão, o failover do ouvinte somente leitura é desabilitado. Isso garante que o desempenho do primário não seja afetado quando o secundário estiver offline. No entanto, isso também significa que as sessões somente leitura não poderão conectar-se até que o secundário seja recuperado. Se você não puder tolerar o tempo de inatividade para as sessões somente leitura e estiver OK para usar temporariamente o primário para tráfego somente leitura e de leitura/gravação às custas da degradação de desempenho potencial do primário, você poderá habilitar o failover para o ouvinte somente leitura Configurando a propriedade `AllowReadOnlyFailoverToPrimary`. Nesse caso, o tráfego somente leitura será redirecionado automaticamente para o primário se o secundário não estiver disponível.

- **Failover planejado**

   O failover planejado executa uma sincronização completa entre o banco de dados primário e o secundário antes de o secundário mudar para a função de primário. Isso assegura que não ocorra nenhuma perda de dados. O failover planejado é usado nos seguintes cenários:

  - Executar a recuperação de desastre em produção quando a perda de dados não é aceitável
  - Relocar os bancos de dados para uma região diferente
  - Retorne os bancos de dados para a região primária após a interrupção ter sido atenuada (failback).

- **Failover não planejado**

   Um failover forçado ou não planejado mudará imediatamente o secundário para a função primária, sem nenhuma sincronização com o primário. Esta operação pode resultar em perda de dados. Um failover não planejado é usado como um método de recuperação durante as interrupções quando o primário não está acessível. Quando o primário original estiver online novamente, ele será reconectado automaticamente sem sincronização e se tornará um novo secundário.

- **Failover manual**

  Você pode iniciar o failover manualmente a qualquer momento, independentemente da configuração de failover automático. Se a política de failover automático não for configurada, será necessário fazer o failover manual para recuperar os bancos de dados no grupo de failover para o secundário. Você pode iniciar um failover forçado ou amigável (com sincronização total de dados). O failover manual pode ser usado para relocar o primário para a região secundária. Quando o failover estiver concluído os registros DNS são atualizados automaticamente para garantir a conectividade com o novo primário

- **Período de carência com perda de dados**

  Como os bancos de dados primário e secundário são sincronizados usando replicação assíncrona, o failover pode resultar em perda de dados. Você pode personalizar a política de failover automático para refletir a tolerância do seu aplicativo à perda de dados. Ao configurar `GracePeriodWithDataLossHours`, você pode controlar quanto tempo o sistema aguarda antes de iniciar o failover que provavelmente resultará em perda de dados.

- **Vários grupos de failover**

  Você pode configurar vários grupos de failover para o mesmo par de servidores para controlar a escala de failovers. Cada grupo sofre failover de forma independente. Se seu aplicativo multilocatário usa pools elásticos, você pode usar esse recurso para misturar os bancos de dados primários e secundários em cada pool. Dessa forma, você pode reduzir o impacto de uma interrupção a somente metade dos locatários.

  > [!NOTE]
  > A Instância Gerenciada não dá suporte a vários grupos de failover.
  
## <a name="permissions"></a>Permissões

As permissões para um grupo de failover são gerenciadas por [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md). A função [colaborador de SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) tem todas as permissões necessárias para gerenciar grupos de failover.

### <a name="create-failover-group"></a>Criar grupo de failover

Para criar um grupo de failover, você precisa de acesso de gravação de RBAC para os servidores primários e secundários e para todos os bancos de dados no grupo de failover. Para uma instância gerenciada, você precisa de acesso de gravação de RBAC para a instância gerenciada primária e secundária, mas as permissões em bancos de dados individuais não são relevantes, pois bancos de dados individuais de instância gerenciada não podem ser adicionados ou removidos de um grupo de failover. 

### <a name="update-a-failover-group"></a>Atualizar um grupo de failover

Para atualizar um grupo de failover, você precisa de acesso de gravação do RBAC para o grupo de failover e de todos os bancos de dados no servidor primário atual ou instância gerenciada.  

### <a name="failover-a-failover-group"></a>Failover de um grupo de failover

Para fazer failover de um grupo de failover, você precisa de acesso de gravação de RBAC ao grupo de failover no novo servidor primário ou instância gerenciada.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Práticas recomendadas de como usar grupos de failover com bancos de dados individuais e pools elásticos

O grupo de failover automático precisa ser configurado no servidor do Banco de Dados SQL primário e o conectará ao servidor do Banco de Dados SQL secundário em outra região do Azure. Os grupos podem incluir alguns ou todos os bancos de dados nesses servidores. O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando vários bancos de dados e um grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Consulte [Adicionar um único banco de dados a um grupo de failover](sql-database-single-database-failover-group-tutorial.md) para obter um tutorial passo a passo detalhado adicionando um banco de dados individual a um grupo de failover.

Ao projetar um serviço pensando em continuidade de negócios, siga estas diretrizes gerais:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Usando um ou vários grupos de failover para gerenciar o failover de vários bancos de dados

Um ou mais grupos de failover podem ser criados entre dois servidores em diferentes regiões (servidores primário e secundário). Cada grupo pode conter um ou vários bancos de dados que são recuperados como uma unidade no caso de alguns ou todos os bancos de dados primários ficarem indisponíveis devido a uma interrupção na região primária. O grupo de failover cria um banco de dados geograficamente secundário com o mesmo objetivo de serviço do primário. Se você adicionar uma relação de replicação geográfica existente ao grupo de failover, certifique-se de que o geograficamente secundário esteja configurado com o mesmo nível de serviço e tamanho da computação do primário.
  
> [!IMPORTANT]
> A criação de grupos de failover entre dois servidores em assinaturas diferentes não tem suporte no momento para bancos de dados individuais e pools elásticos. Se você mover o servidor primário ou secundário para uma assinatura diferente depois que o grupo de failover tiver sido criado, isso poderá resultar em falhas das solicitações de failover e outras operações.

### <a name="using-read-write-listener-for-oltp-workload"></a>Usando o ouvinte de leitura/gravação para carga de trabalho OLTP

Ao executar operações de OLTP, use `<fog-name>.database.windows.net` como a URL do servidor e as conexões são direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. Observe que o failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS.

### <a name="using-read-only-listener-for-read-only-workload"></a>Usando o ouvinte somente leitura para carga de trabalho somente leitura

Se houver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualizações de dados, você poderá usar o banco de dados secundário no aplicativo. Para sessões somente leitura, use `<fog-name>.secondary.database.windows.net` como a URL do servidor e a conexão é direcionada automaticamente para o secundário. Também é recomendável que você indique na tentativa de leitura da cadeia de conexão usando `ApplicationIntent=ReadOnly`. Se você quiser garantir que a carga de trabalho somente leitura possa se reconectar após o failover ou, caso o servidor secundário fique offline, certifique-se de configurar a propriedade `AllowReadOnlyFailoverToPrimary` da política de failover.

### <a name="preparing-for-performance-degradation"></a>Preparando-se para degradação do desempenho

Um aplicativo típico do Azure usa vários serviços do Azure e consiste em vários componentes. O failover automatizado do grupo de failover é disparado com base no estado que os componentes do SQL Azure são sozinhos. Outros serviços do Azure na região primária podem não ser afetados pela interrupção e seus componentes ainda podem estar disponíveis nessa região. Depois que os bancos de dados primários mudarem para a região de DR, a latência entre os componentes dependentes poderá aumentar. Para evitar o impacto da maior latência no desempenho do aplicativo, garanta a redundância de todos os componentes do aplicativo na região de recuperação de desastres e siga essas [diretrizes de segurança de rede](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Preparando para perda de dados

Se uma interrupção for detectada, o SQL aguardará o período especificado por `GracePeriodWithDataLossHours`. O valor padrão é de 1 hora. Se você não puder perder dados, certifique-se de definir `GracePeriodWithDataLossHours` para um número suficientemente grande, como 24 horas. Use o failover manual do grupo para fazer failback do secundário para primário.

> [!IMPORTANT]
> Os pools elásticos com 800 ou menos DTUs e mais de 250 bancos de dados usando a replicação geográfica podem encontrar problemas, incluindo failovers planejados mais longos e diminuição do desempenho.  A ocorrência desses problemas é mais provável para cargas de trabalho com uso intensivo de gravação, quando os pontos de extremidade de replicação geográfica são separados por uma grande extensão geográfica ou quando vários pontos de extremidade secundários são usados para cada banco de dados.  Os sintomas desses problemas são indicados quando o retardo da replicação geográfica aumenta ao longo do tempo.  Esse retardo pode ser monitorado usando [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se esses problemas ocorrerem, considere mitigações como aumentar o número de DTUs do pool ou reduzir o número de bancos de dados replicados geograficamente no mesmo pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Alterando a região secundária do grupo de failover

Para ilustrar a sequência de alteração, vamos pressupor que o servidor A é o servidor primário, o servidor B é o servidor secundário existente e o servidor C é o novo secundário na terceira região.  Para fazer a transição, siga estas etapas:

1.  Crie secundários adicionais de cada banco de dados no servidor A para o servidor C usando a [replicação geográfica ativa](sql-database-active-geo-replication.md). Cada banco de dados no servidor A terá dois secundários, um no servidor B e outro no servidor C. Isso garantirá que os bancos de dados primários permaneçam protegidos durante a transição.
2.  Exclua o grupo de failover. Neste ponto, os logons falharão. Isso ocorre porque os aliases do SQL para os ouvintes do grupo de failover foram excluídos e o gateway não reconhecerá o nome do grupo de failover.
3.  Crie novamente o grupo de failover com o mesmo nome entre os servidores A e C. Neste ponto, os logons deixarão de falhar.
4.  Adicione todos os bancos de dados primários no servidor a para o novo grupo de failover.
5.  Remova o servidor B. Todos os bancos de dados em B serão excluídos automaticamente. 


### <a name="changing-primary-region-of-the-failover-group"></a>Alterando a região primária do grupo de failover

Para ilustrar a sequência de alteração, vamos pressupor que o servidor A é o servidor primário, o servidor B é o servidor secundário existente e o servidor C é o novo primário na terceira região.  Para fazer a transição, siga estas etapas:

1.  Execute um failover planejado para alternar o servidor primário para B. o servidor A se tornará o novo servidor secundário. O failover pode resultar em vários minutos de tempo de inatividade. O tempo real dependerá do tamanho do grupo de failover.
2.  Crie secundários adicionais de cada banco de dados no servidor B para o servidor C usando [a replicação geográfica ativa](sql-database-active-geo-replication.md). Cada banco de dados no servidor B terá dois secundários, um no servidor A e outro no servidor C. Isso garantirá que os bancos de dados primários permaneçam protegidos durante a transição.
3.  Exclua o grupo de failover. Neste ponto, os logons falharão. Isso ocorre porque os aliases do SQL para os ouvintes do grupo de failover foram excluídos e o gateway não reconhecerá o nome do grupo de failover.
4.  Crie novamente o grupo de failover com o mesmo nome entre os servidores A e C. Neste ponto, os logons deixarão de falhar.
5.  Adicione todos os bancos de dados primários no B ao novo grupo de failover. 
6.  Execute um failover planejado do grupo de failover para o comutador B e C. Agora, o servidor C se tornará o primário e o B-o secundário. Todos os bancos de dados secundários no servidor A serão vinculados automaticamente aos primários em C. Como na etapa 1, o failover pode resultar em vários minutos de inatividade.
6.  Descarte o servidor a. Todos os bancos de dados em um serão excluídos automaticamente.

> [!IMPORTANT]
> Quando o grupo de failover é excluído, os registros DNS dos pontos de extremidade do ouvinte também são excluídos. Nesse ponto, há uma probabilidade diferente de zero de outra pessoa que cria um grupo de failover ou alias de servidor com o mesmo nome, o que o impedirá de usá-lo novamente. Para minimizar o risco, não use nomes de grupos de failover genéricos.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Práticas recomendadas de uso de grupos de failover com instâncias gerenciadas

O grupo de failover automático precisa ser configurado na instância primária e a conectará à instância secundária em uma região do Azure diferente.  Todos os bancos de dados na instância serão replicados para a instância secundária.

O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando uma instância gerenciada e um grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Consulte [Adicionar instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md) para obter um tutorial passo a passo detalhado adicionando uma instância gerenciada para usar o grupo de failover.

Se seu aplicativo usar a instância gerenciada como a camada de dados, siga estas diretrizes gerais ao projetar para continuidade dos negócios:

### <a name="creating-the-secondary-instance"></a>Criando a instância secundária 

Para garantir conectividade ininterrupta à instância primária após o failover, ambas as instâncias primária e secundária precisam estar na mesma zona DNS. Ele garantirá que o mesmo certificado de vários domínios (SAN) possa ser usado para autenticar as conexões do cliente com uma das duas instâncias no grupo de failover. Quando seu aplicativo está pronto para implantação em produção, crie uma instância do secundário em uma região diferente e assegure que ela compartilhe a zona DNS com a instância do primário. Você pode fazer isso especificando um `DNS Zone Partner` parâmetro opcional usando o portal do Azure, o PowerShell ou a API REST.

> [!IMPORTANT]
> A primeira instância criada na sub-rede determina a zona DNS para todas as instâncias subsequentes na mesma sub-rede. Isso significa que duas instâncias da mesma sub-rede não podem pertencer a diferentes zonas DNS.

Para obter mais informações sobre como criar a instância secundária na mesma zona DNS que a instância primária, consulte [criar uma instância gerenciada secundária](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

### <a name="enabling-replication-traffic-between-two-instances"></a>Habilitando o tráfego de replicação entre duas instâncias

Já que cada instância é isolada em sua própria rede virtual, o tráfego em duas vias entre essas redes virtuais deve ser permitido. Confira [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Criando um grupo de failover entre instâncias gerenciadas em assinaturas diferentes

Você pode criar um grupo de failover entre instâncias gerenciadas em duas assinaturas diferentes. Ao usar a API do PowerShell, você pode fazer isso especificando o parâmetro `PartnerSubscriptionId` para a instância secundária. Ao usar a API REST, cada ID de instância incluída no parâmetro `properties.managedInstancePairs` pode ter sua própria SubscriptionId.
  
> [!IMPORTANT]
> O portal do Azure não oferece suporte a grupos de failover em assinaturas diferentes.

### <a name="managing-failover-to-secondary-instance"></a>Gerenciando o failover para a instância secundária

O grupo de failover gerenciará o failover de todos os bancos de dados na instância. Quando um grupo é criado, cada banco de dados na instância será replicado geograficamente de modo automático para a instância do secundário. Não é possível usar grupos de failover para iniciar um failover parcial de um subconjunto dos bancos de dados.

> [!IMPORTANT]
> Se um banco de dados for removido da instância do primário, ele será também removido automaticamente na instância geográfica do secundário.

### <a name="using-read-write-listener-for-oltp-workload"></a>Usando o ouvinte de leitura/gravação para carga de trabalho OLTP

Ao executar operações de OLTP, use `<fog-name>.zone_id.database.windows.net` como a URL do servidor e as conexões são direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. O failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS. Como a instância secundária compartilha a zona DNS com o primário, o aplicativo cliente poderá se reconectar a ela usando o mesmo certificado de SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Usando o ouvinte somente leitura para se conectar à instância secundária

Se houver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualizações de dados, você poderá usar o banco de dados secundário no aplicativo. Para se conectar diretamente ao secundário com replicação geográfica, use `server.secondary.zone_id.database.windows.net` como a URL do servidor.

> [!NOTE]
> Em determinadas camadas de serviço, o Banco de Dados SQL do Azure é compatível com o uso de [réplicas somente leitura](sql-database-read-scale-out.md) para realizar o balanceamento de cargas de trabalho de consulta somente leitura usando a capacidade de uma réplica somente leitura e usando o parâmetro `ApplicationIntent=ReadOnly` na cadeia de conexão. Quando você tiver configurado um secundário replicado geograficamente, você pode usar essa funcionalidade para se conectar a uma réplica somente leitura na localização do primário ou na localização com replicação geográfica.
> - Para se conectar a uma réplica somente leitura na localização do primário, use `<fog-name>.zone_id.database.windows.net`.
> - Para se conectar a uma réplica somente leitura no local secundário, use `<fog-name>.secondary.zone_id.database.windows.net`.

### <a name="preparing-for-performance-degradation"></a>Preparando-se para degradação do desempenho

Um aplicativo típico do Azure usa vários serviços do Azure e consiste em vários componentes. O failover automatizado do grupo de failover é disparado com base no estado que os componentes do SQL Azure são sozinhos. Outros serviços do Azure na região primária podem não ser afetados pela interrupção e seus componentes ainda podem estar disponíveis nessa região. Depois que os bancos de dados primários mudarem para a região de DR, a latência entre os componentes dependentes poderá aumentar. Para evitar o impacto da maior latência no desempenho do aplicativo, garanta a redundância de todos os componentes do aplicativo na região de recuperação de desastres e siga essas [diretrizes de segurança de rede](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Preparando para perda de dados

Quando uma falha for detectada, o SQL disparará o failover de leitura-gravação se não houver perda de dados, até onde nós sabemos. Caso contrário, ele aguardará o período especificado por você em `GracePeriodWithDataLossHours`. Se você especificou `GracePeriodWithDataLossHours`, esteja preparado para perda de dados. Em geral, durante interrupções, o Azure favorece a disponibilidade. Se você não puder perder dados, defina GracePeriodWithDataLossHours com um número grande o suficiente, como 24 horas.

A atualização do DNS do ouvinte de leitura-gravação ocorrerá imediatamente após o início do failover. Esta operação não resultará em perda de dados. No entanto, o processo de mudar as funções de bancos de dados pode levar até 5 minutos em condições normais. Até que ele seja concluído, alguns bancos de dados na nova instância do primário ainda serão somente leitura. Se o failover for iniciado usando o PowerShell, toda a operação será síncrona. Se ele for iniciado usando o portal do Azure, a interface do usuário indicará o status de conclusão. Se ele é iniciado usando a API REST, use o mecanismo de sondagem padrão do Azure Resource Manager para monitorar quanto à conclusão.

> [!IMPORTANT]
> Use o failover manual de grupo para mover os primários de volta para a localização original. Quando a interrupção que causou o failover for atenuada, você poderá mover seus bancos de dados primários para a localização original. Para fazer isso, você deve iniciar o failover manual do grupo.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Alterando a região secundária do grupo de failover

Vamos supor que a instância A é a instância primária, a instância B é a instância secundária existente e a instância C é a nova instância secundária na terceira região.  Para fazer a transição, siga estas etapas:

1.  Crie a instância C com o mesmo tamanho de e na mesma zona DNS. 
2.  Exclua o grupo de failover entre as instâncias A e B. Neste ponto, os logons falharão porque os aliases do SQL para os ouvintes do grupo de failover foram excluídos e o gateway não reconhecerá o nome do grupo de failover. Os bancos de dados secundários serão desconectados dos primários e se tornarão bancos de dados de leitura/gravação. 
3.  Crie um grupo de failover com o mesmo nome entre a instância A e C. siga as instruções no [tutorial grupo de failover com instância gerenciada](sql-database-managed-instance-failover-group-tutorial.md). Essa é uma operação de tamanho de dados e será concluída quando todos os bancos de dado da instância A forem propagados e sincronizados.
4.  Exclua a instância B se não for necessário para evitar encargos desnecessários.

> [!NOTE]
> Após a etapa 2 e até que a etapa 3 seja concluída, os bancos de dados na instância A permanecerão desprotegidos contra uma falha catastrófica da instância A.

### <a name="changing-primary-region-of-the-failover-group"></a>Alterando a região primária do grupo de failover

Vamos supor que a instância A é a instância primária, a instância B é a instância secundária existente e a instância C é a nova instância primária na terceira região.  Para fazer a transição, siga estas etapas:

1.  Crie a instância C com o mesmo tamanho que B e na mesma zona DNS. 
2.  Conecte-se à instância B e failover manual para alternar a instância primária para B. a instância A se tornará a nova instância secundária automaticamente.
3.  Exclua o grupo de failover entre as instâncias A e B. Neste ponto, os logons falharão porque os aliases do SQL para os ouvintes do grupo de failover foram excluídos e o gateway não reconhecerá o nome do grupo de failover. Os bancos de dados secundários serão desconectados dos primários e se tornarão bancos de dados de leitura/gravação. 
4.  Crie um grupo de failover com o mesmo nome entre a instância A e C. siga as instruções no [tutorial grupo de failover com instância gerenciada](sql-database-managed-instance-failover-group-tutorial.md). Essa é uma operação de tamanho de dados e será concluída quando todos os bancos de dado da instância A forem propagados e sincronizados.
5.  Exclua A instância A se não for necessário para evitar encargos desnecessários.

> [!NOTE] 
> Após a etapa 3 e até que a etapa 4 seja concluída, os bancos de dados na instância A permanecerão desprotegidos contra uma falha catastrófica da instância A.

> [!IMPORTANT]
> Quando o grupo de failover é excluído, os registros DNS dos pontos de extremidade do ouvinte também são excluídos. Nesse ponto, há uma probabilidade diferente de zero de outra pessoa que cria um grupo de failover ou alias de servidor com o mesmo nome, o que o impedirá de usá-lo novamente. Para minimizar o risco, não use nomes de grupos de failover genéricos.

## <a name="failover-groups-and-network-security"></a>Grupos de failover e a segurança de rede

Para alguns aplicativos, as regras de segurança exigem que o acesso à rede para a camada de dados seja restrito a um componente ou componentes específicos, como uma VM, um serviço Web, etc. Esse requisito apresenta alguns desafios para o design de continuidade de negócios e o uso dos grupos de failover. Considere as seguintes opções ao implementar esse acesso restrito.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Como usar grupos de failover e regras da rede virtual

Se você está usando [Pontos de extremidade e regras de serviço de Rede Virtual](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso ao seu banco de dados SQL, lembre-se de que cada ponto de extremidade de serviço de Rede Virtual se aplica a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem a comunicação da sub-rede. Portanto, apenas os aplicativos implantados na mesma região do cliente podem se conectar ao banco de dados primário. Uma vez que o failover resulta em sessões de cliente SQL serem redirecionadas para um servidor em uma região diferente (secundária), essas sessões falham se originadas de um cliente fora dessa região. Por esse motivo, a política de failover automático não poderá ser habilitada se os servidores participantes estiverem incluídos nas regras de Rede Virtual. Para dar suporte a failover manual, siga estas etapas:

1. Provisione as cópias redundantes dos componentes front-end do seu aplicativo (serviço Web, máquinas virtuais etc.) na região secundária
2. Configurar as [regras da rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) individualmente para os servidores primário e secundário
3. Habilitar o [failover front-end usando uma configuração do Gerenciador de tráfego](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Iniciar o failover manual quando a interrupção for detectada. Essa opção é otimizada para os aplicativos que precisam de latência consistente entre o front-end e a camada de dados e oferece suporte à recuperação quando o front-end, a camada de dados ou ambos são afetados pela interrupção.

> [!NOTE]
> Se você estiver usando o **ouvinte somente leitura** para balanceamento de uma carga de trabalho somente leitura, essa carga de trabalho deverá ser executada em uma VM ou outro recurso na região secundária para que possa se conectar ao banco de dados secundário.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Usando grupos de failover e regras de firewall de banco de dados SQL

Se o seu plano de continuidade de negócios exigir failover usando grupos com failover automático, você poderá restringir o acesso ao banco de dados SQL usando as regras de firewall tradicional. Para dar suporte a failover automático, siga estas etapas:

1. [Criar um IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Crie um balanceador de carga público](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) e atribua o IP público a ele.
3. [Crie uma rede virtual e as máquinas virtuais](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) para os componentes de front-end
4. [Crie um grupo de segurança de rede](../virtual-network/security-overview.md) e configure conexões de entrada.
5. Verifique se as conexões de saída estão abertas para o banco de dados SQL do Azure usando a [marca de serviço](../virtual-network/security-overview.md#service-tags) 'Sql'.
6. Crie uma [regra de firewall de banco de dados SQL](sql-database-firewall-configure.md) para permitir o tráfego de entrada do endereço IP público criado na etapa 1.

Para obter mais informações sobre como configurar o acesso de saída e qual IP usar nas regras de firewall, veja [conexões de saída do balanceador de carga](../load-balancer/load-balancer-outbound-connections.md).

A configuração acima garantirá que o failover automático não bloqueie conexões dos componentes front-end e pressupõe que o aplicativo pode tolerar a latência mais longa entre o front-end e a camada de dados.

> [!IMPORTANT]
> Para garantir a continuidade dos negócios para interrupções regionais, garanta redundância geográfica para bancos de dados e componentes de front-end.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Habilitando a replicação geográfica entre instâncias gerenciadas e suas VNets

Quando você configura um grupo de failover entre instâncias gerenciadas primárias e secundárias em duas regiões diferentes, cada instância é isolada usando uma rede virtual independente. Para permitir o tráfego de replicação entre esses VNets, verifique se esses pré-requisitos foram atendidos:

1. As duas instâncias gerenciadas precisam estar em regiões diferentes do Azure.
2. As duas instâncias gerenciadas precisam ser da mesma camada de serviço e ter o mesmo tamanho de armazenamento.
3. Sua instância gerenciada secundária deve estar vazia (nenhum banco de dados de usuário).
4. As redes virtuais usadas pelas instâncias gerenciadas precisam ser conectadas por meio de um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou de uma [rota expressa](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Quando duas redes virtuais se conectam por meio de uma rede local, verifique se não há nenhuma regra de firewall bloqueando as portas 5022 e 11000-11999. O Emparelhamento VNET Global não é compatível.
5. Os dois VNets de instância gerenciada não podem ter endereços IP sobrepostos.
6. Você precisa configurar seus NSG (grupos de segurança de rede) de modo que as portas 5022 e o intervalo 11000 ~ 12000 sejam abertos de entrada e saída para conexões da sub-rede da outra instância gerenciada. Isso é para permitir o tráfego de replicação entre as instâncias.

   > [!IMPORTANT]
   > Regras de segurança de NSG mal configuradas resultam em operações de cópia de banco de dados paralisadas.

7. A instância secundária está configurada com a ID de zona DNS correta. A zona DNS é uma propriedade de uma instância gerenciada e um cluster virtual, e sua ID é incluída no endereço de nome do host. A ID da zona é gerada como uma cadeia de caracteres aleatória quando a primeira instância gerenciada é criada em cada VNet e a mesma ID é atribuída a todas as outras instâncias na mesma sub-rede. Uma vez atribuída, a zona DNS não pode ser modificada. As instâncias gerenciadas incluídas no mesmo grupo de failover devem compartilhar a zona DNS. Isso é feito passando a ID da zona da instância primária como o valor do parâmetro DnsZonePartner ao criar a instância secundária. 

   > [!NOTE]
   > Para obter um tutorial detalhado sobre como configurar grupos de failover com a instância gerenciada, consulte [Adicionar uma instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou fazer downgrade de um banco de dados primário

Você pode atualizar ou fazer downgrade de um banco de dados primário para um tamanho da computação diferente (dentro da mesma camada de serviço, não entre Uso Geral e Comercialmente Crítico) sem desconectar nenhum banco de dados secundário. Ao atualizar, recomendamos que você atualize todos os bancos de dados secundários primeiro e, em seguida, atualize o primário. Ao fazer downgrade, inverta o pedido: faça o downgrade do primário primeiro e, em seguida, downgrade todos os bancos de dados secundários. Quando você atualiza ou faz downgrade do banco de dados para uma camada de serviço diferente essa recomendação é imposta.

Essa sequência é recomendada especificamente para evitar o problema em que o secundário em uma SKU inferior é sobrecarregado e deve ser propagado novamente durante um processo de atualização ou de downgrade. Você também pode evitar o problema tornando o primário somente leitura, às custas de afetar todas as cargas de trabalho de leitura/gravação no primário.

> [!NOTE]
> Se você tiver criado um banco de dados secundário como parte da configuração do grupo de failover não é recomendável fazer o downgrade do banco de dados secundário. Isso é para garantir que sua camada de dados tenha capacidade suficiente para processar sua carga de trabalho normal após o failover ser ativado.

## <a name="preventing-the-loss-of-critical-data"></a>Evitando a perda de dados críticos

Devido à alta latência das redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. A replicação assíncrona tornará a perda de alguns dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir nenhuma perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a confirmação da transação. Chamar `sp_wait_for_database_copy_sync` bloqueia o thread de chamada até que a última transação confirmada seja transmitida para o banco de dados secundário. Contudo, a chamada não aguarda as transações transmitidas serem reproduzidas e confirmadas no banco de dados secundário. `sp_wait_for_database_copy_sync` está no escopo de um link de cópia contínua específico. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

> [!NOTE]
> `sp_wait_for_database_copy_sync` impede a perda de dados após o failover, mas não garante a sincronização completa para acesso de leitura. O atraso causado por uma chamada de procedimento `sp_wait_for_database_copy_sync` pode ser significativo e depende do tamanho do log de transações no momento da chamada.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de failover e restauração pontual

Para obter informações sobre como usar a restauração pontual com grupos de failover, confira a [PITR (recuperação pontual)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Limitações de grupos de failover

Esteja ciente das seguintes limitações:

- O grupo de failover não pode ser criado entre dois servidores ou instâncias nas mesmas regiões do Azure.
- O grupo de failover não pode ser renomeado. Será necessário excluir o grupo e recriá-lo com um nome diferente. 
- Não há suporte para renomeação de banco de dados para instâncias no grupo de failover. Você precisará excluir temporariamente o grupo de failover para poder renomear um banco de dados.

## <a name="programmatically-managing-failover-groups"></a>Gerenciando os grupos de failover programaticamente

Conforme discutido anteriormente, os grupos de failover automático e a replicação geográfica ativa podem ser gerenciados programaticamente usando o Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis. A replicação geográfica ativa inclui um conjunto de APIs do Azure Resource Manager para gerenciamento, incluindo a [API REST do Banco de Dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs exigem o uso de grupos de recursos e dão suporte a RBAC (segurança baseada em funções). Para obter mais informações sobre como implementar funções de acesso, confira [Controle de Acesso Baseado em Funções do Azure](../role-based-access-control/overview.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gerenciar failover do Banco de Dados SQL com pools elásticos e bancos de dados individuais

| Cmdlet | Description |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Remove um grupo de failover do servidor |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera a configuração de um grupo de failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica a configuração de um grupo de failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Dispara o failover de um grupo de failover para o servidor secundário |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Adiciona um ou mais bancos de dados a um grupo de failover|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gerenciar grupos de failover do banco de dados SQL com instâncias gerenciadas

| Cmdlet | Description |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Este comando cria um grupo de failover e o registra em ambas as instâncias primárias e secundárias|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica a configuração de um grupo de failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera a configuração de um grupo de failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Dispara o failover de um grupo de failover para a instância secundária|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Remove um grupo de failover|

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gerenciar failover do Banco de Dados SQL com pools elásticos e bancos de dados individuais

| Comando | Description |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [AZ SQL failover-Group Delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Remove um grupo de failover do servidor |
| [AZ SQL failover-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Recupera uma configuração de grupo de failover |
| [AZ SQL failover – atualização do grupo](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifica a configuração de um grupo de failover e/ou adiciona um ou mais bancos de dados a um grupo de failover|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Dispara o failover de um grupo de failover para o servidor secundário |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gerenciar grupos de failover do banco de dados SQL com instâncias gerenciadas

| Comando | Description |
| --- | --- |
| [AZ SQL Instance-failover-criar grupo](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Este comando cria um grupo de failover e o registra em ambas as instâncias primárias e secundárias |
| [AZ SQL Instance-failover-atualização de grupo](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Modifica a configuração de um grupo de failover|
| [AZ SQL Instance-failover-Mostrar grupo](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Recupera a configuração de um grupo de failover|
| [AZ SQL Instance-failover-conjunto de grupos-primário](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Dispara o failover de um grupo de failover para a instância secundária|
| [AZ SQL Instance-failover-excluir grupo](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Remove um grupo de failover |

* * *

> [!IMPORTANT]
> Para um script de exemplo, confira [Configurar e realizar o failover de um grupo de failover para um banco de dados individual](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>API REST: gerenciar grupos de failover do banco de dados SQL com bancos de dados únicos e em pool

| API | Description |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Criar ou atualizar grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Remove um grupo de failover do servidor |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Dispara o failover do servidor primário atual para o servidor secundário com sincronização de dados completa.|
| [O Failover forçado permite a perda de dados](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Dispara o failover do servidor primário atual para o servidor secundário sem sincronizar dados. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Recupera a configuração de um grupo de failover. |
| [Listar grupos de failover pelo servidor](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Lista os grupos de failover em um servidor. |
| [Atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Atualiza a configuração de um grupo de failover. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>API REST: gerenciar grupos de failover com instâncias gerenciadas

| API | Description |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Criar ou atualizar a configuração de um grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Remove um grupo de failover da instância |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Dispara o failover da instância primária atual para esta instância com sincronização de dados completa. |
| [O Failover forçado permite a perda de dados](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Dispara o failover da instância primária atual para a instância secundária sem sincronizar dados. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Recupera a configuração de um grupo de failover. |
| [Listar grupos de failover – listar por localização](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Lista os grupos de failover em uma localização. |

## <a name="next-steps"></a>Próximos passos

- Para obter tutoriais detalhados, consulte
    - [Adicionar um banco de dados individual a um grupo de failover](sql-database-single-database-failover-group-tutorial.md)
    - [Adicionar pool elástico a um grupo de failover](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Adicionar uma instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md)
- Para exemplos de scripts, consulte:
  - [Usar o PowerShell para configurar a replicação geográfica ativa para um banco de dados individual no banco de dados SQL do Azure](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Usar o PowerShell para configurar a replicação geográfica ativa para um banco de dados em pool no banco de dados SQL do Azure](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Usar o PowerShell para adicionar um banco de dados individual do banco de dados SQL do Azure a um grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre como usar backups automatizados de recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre requisitos de autenticação para um novo servidor primário e banco de dados, consulte [Segurança do Banco de Dados SQL após a recuperação de desastres](sql-database-geo-replication-security-config.md).
