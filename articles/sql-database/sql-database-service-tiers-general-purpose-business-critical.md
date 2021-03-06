---
title: Uso geral e crítico para os negócios
description: O artigo discute as camadas de serviço de uso geral e crítico para os negócios no modelo de compra baseado em vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/23/2020
ms.openlocfilehash: fab24d55509ab315775437ca343e35fc90174f63
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715097"
---
# <a name="azure-sql-database-service-tiers"></a>Camadas de serviço do Banco de Dados SQL do Azure

O banco de dados SQL do Azure baseia-se na arquitetura do mecanismo de banco de dados SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, mesmo se houver uma falha de infraestrutura. Três camadas de serviço são usadas no banco de dados SQL do Azure, cada uma com um modelo de arquitetura diferente. Essas camadas de serviço são:

- [Uso geral](sql-database-service-tier-general-purpose.md), que é projetado para cargas de trabalho orientadas a orçamento.
- [Hiperescala](sql-database-service-tier-hyperscale.md), projetada para a maioria das cargas de trabalho de negócios, fornecendo armazenamento altamente escalonável, expansão de leitura e recursos de restauração rápida de banco de dados.
- [Comercialmente crítico](sql-database-service-tier-business-critical.md), projetado para cargas de trabalho de baixa latência com alta resiliência a falhas e failovers rápidos.

Este artigo discute as diferenças entre as considerações de camadas de serviço, armazenamento e backup para as camadas de serviço de uso geral e crítico para os negócios no modelo de compra baseado em vCore.

## <a name="service-tier-comparison"></a>Comparação da camada de serviço

A tabela a seguir descreve as principais diferenças entre as camadas de serviço para a geração mais recente (Gen5). Observe que as características da camada de serviço podem ser diferentes em Banco de Dados Individual e Instância Gerenciada.

| | Tipo de recurso | Propósito geral |  Hiperescala | Comercialmente Crítico |
|:---:|:---:|:---:|:---:|:---:|
| **Mais adequado para** | |  Oferece opções equilibradas de computação e armazenamento orientadas ao orçamento. | A maioria das cargas de trabalho comerciais. Dimensionamento automático do tamanho de armazenamento de até 100 TB, dimensionamento vertical e horizontal de computação de fluxo, restauração rápida de banco de dados. | Aplicativos OLTP com alta taxa de transação e baixa latência de e/s. Oferece maior resiliência a falhas e failovers rápidos usando várias réplicas atualizadas de forma síncrona.|
|  **Disponível no tipo de recurso:** ||Banco de dados único / Elástico pool / instância gerenciada | Banco de dados individual | Banco de dados único / Elástico pool / instância gerenciada |
| **Tamanho da computação**|Banco de dados único / Elástico pool | 1 a 80 vCores | 1 a 80 vCores | 1 a 80 vCores |
| | Instância gerenciada | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Pools de instâncias gerenciadas | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | N/D |
| **Tipo de armazenamento** | Tudo | Armazenamento remoto Premium (por instância) | Armazenamento desacoplado com cache SSD local (por instância) | Armazenamento SSD local super rápido (por instância) |
| **Tamanho do banco de dados** | Banco de dados único / Elástico pool | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerenciada  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **Tamanho de armazenamento** | Banco de dados único / Elástico pool | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerenciada  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **Tamanho do TempDB** | Banco de dados único / Elástico pool | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância gerenciada  | [24 GB por vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | Até 4 TB- [limitado pelo tamanho do armazenamento](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Taxa de transferência de gravação de log** | Banco de dados individual | [1,875 MB/s por vCore (máximo de 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s por vCore (máx. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância gerenciada | [3 MB/s por vCore (máximo de 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s por VCORE (máx. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Disponibilidade**|Tudo| 99,99% |  [99,95% com uma réplica secundária, 99,99% com mais réplicas](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% com Banco de dados individual com redundância de zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Backups**|Tudo|RA-GRS, 7-35 dias (7 dias por padrão)| RA-GRS, 7 dias, tempo constante de recuperação point-in-time (PITR) | RA-GRS, 7-35 dias (7 dias por padrão) |
|**OLTP in-memory** | | N/D | N/D | Disponível |
|**Réplicas somente leitura**| | 0 interno <br> 0-4 usando [a replicação geográfica](sql-database-active-geo-replication.md) | 0-4 interno | 1 interno, incluído no preço <br> 0-4 usando [a replicação geográfica](sql-database-active-geo-replication.md) |
|**Preço/cobrança** | Banco de dados individual | [vCore, armazenamento reservado e armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. | [vCore para cada réplica e armazenamento usado](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>IOPS ainda não cobrado. | [vCore, armazenamento reservado e armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. |
|| Instância Gerenciada | o [vCore e o armazenamento reservado](https://azure.microsoft.com/pricing/details/sql-database/managed/) são cobrados. <br/>O IOPS não é cobrado.<br/>O armazenamento de backup ainda não foi cobrado. | N/D | o [vCore e o armazenamento reservado](https://azure.microsoft.com/pricing/details/sql-database/managed/) são cobrados. <br/>O IOPS não é cobrado.<br/>O armazenamento de backup ainda não foi cobrado. | 
|**Modelos de desconto**| | [Instâncias reservadas](sql-database-reserved-capacity.md)<br/>[Benefício híbrido do Azure](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de desenvolvimento/teste)<br/>Assinaturas de desenvolvimento/teste [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Benefício híbrido do Azure](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de desenvolvimento/teste)<br/>Assinaturas de desenvolvimento/teste [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Instâncias reservadas](sql-database-reserved-capacity.md)<br/>[Benefício híbrido do Azure](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de desenvolvimento/teste)<br/>Assinaturas de desenvolvimento/teste [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0023p/)|

Para obter mais informações, consulte as diferenças detalhadas entre as camadas de serviço em [um banco de dados único (VCORE)](sql-database-vcore-resource-limits-single-databases.md), [pools de banco de dados único (VCORE](sql-database-dtu-resource-limits-single-databases.md)), banco de dados [único (DTU](sql-database-dtu-resource-limits-single-databases.md)), [pools de banco de dados único (DTU)](sql-database-dtu-resource-limits-single-databases.md)e páginas de [instância gerenciada](sql-database-managed-instance-resource-limits.md) .

> [!NOTE]
> Para obter informações sobre a camada de serviço de hiperescala no modelo de compra baseado em vCore, consulte [camada de serviço de hiperescala](sql-database-service-tier-hyperscale.md). Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Armazenamento de dados e de log

Os fatores a seguir afetam a quantidade de armazenamento usada para arquivos de dados e de log e aplica-se a Uso Geral e Comercialmente Crítico. Para obter detalhes sobre o armazenamento de dados e de log em hiperescala, consulte [camada de serviço de hiperescala](sql-database-service-tier-hyperscale.md).

- O armazenamento alocado é usado por arquivos de dados (MDF) e arquivos de log (LDF).
- Cada tamanho de computação de banco de dados individual dá suporte a um tamanho máximo de banco de dados, com um tamanho máximo padrão de 32 GB.
- Quando você configura o tamanho necessário do banco de dados individual (o tamanho do arquivo MDF), 30% mais armazenamento adicional é adicionado automaticamente para dar suporte a arquivos LDF.
- O tamanho do armazenamento para uma instância gerenciada do banco de dados SQL deve ser especificado em múltiplos de 32 GB.
- Você pode selecionar qualquer tamanho de banco de dados individual entre 10 GB e o máximo com suporte.
  - Para armazenamento nas camadas de serviço padrão ou de uso geral, aumente ou diminua o tamanho em incrementos de 10 GB.
  - Para armazenamento nas camadas de serviço Premium ou comercialmente crítico, aumente ou diminua o tamanho em incrementos de 250 GB.
- Na camada de serviço de uso geral, `tempdb` usa um SSD anexado e esse custo de armazenamento é incluído no preço vCore.
- Na camada de serviço comercialmente crítica, `tempdb` compartilha o SSD anexado com os arquivos MDF e LDF, e o custo de armazenamento `tempdb` está incluído no preço vCore.

> [!IMPORTANT]
> Você é cobrado pelo armazenamento total alocado para arquivos MDF e LDF.

Para monitorar o tamanho total atual de seus arquivos MDF e LDF, use [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorar o tamanho atual dos arquivos MDF e LDF individuais, use [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento de backups de banco de dados é alocado para dar suporte a recursos de PITR (restauração pontual) e [EPD (retenção de longo prazo)](sql-database-long-term-retention.md) do banco de dados SQL. Esse armazenamento é alocado separadamente para cada banco de dados e faturado como duas cobranças separadas por banco de dados.

- **PITR**: os backups de banco de dados individuais são copiados para o [armazenamento com redundância geográfica com acesso de leitura (ra-grs)](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente à medida que novos backups são criados. O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações, que são copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração do banco de dados e do período de retenção para backups. É possível configurar um período de retenção separado para cada banco de dados entre 7 e 35 dias. Um valor mínimo de armazenamento igual a 100 por cento (1x) do tamanho do banco de dados é fornecido sem custo adicional. Para a maioria dos bancos de dados, esse valor é suficiente para armazenar 7 dias de backups.
- **EPD**: o banco de dados SQL oferece a opção de configurar a retenção de longo prazo de backups completos por até 10 anos. Se você configurar uma política EPD, esses backups serão armazenados automaticamente no armazenamento RA-GRS, mas você poderá controlar a frequência com que os backups são copiados. Para atender aos diferentes requisitos de conformidade, você pode selecionar períodos de retenção diferentes para backups semanais, mensais e/ou anuais. A configuração escolhida determina a quantidade de armazenamento que será usada para backups EPD. Para estimar o custo do armazenamento EPD, você pode usar a calculadora de preços EPD. Para obter mais informações, consulte [retenção de longo prazo do banco de dados SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Próximos passos

- Para obter detalhes sobre os tamanhos de computação específicos e os tamanhos de armazenamento disponíveis para um único banco de dados nas camadas de serviço de uso geral e crítico para os negócios, consulte [limites de recursos baseados em vCore do banco de dados SQL para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md).
- Para obter detalhes sobre os tamanhos de computação e tamanhos de armazenamento específicos disponíveis para pools elásticos nas camadas de serviço de uso geral e crítico para os negócios, consulte [limites de recursos baseados em vCore do banco de dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).
