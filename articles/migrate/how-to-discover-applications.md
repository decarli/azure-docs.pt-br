---
title: Descobrir aplicativos, funções e recursos em servidores locais com as migrações para Azure
description: Saiba como descobrir aplicativos, funções e recursos em servidores locais com a avaliação de servidor de migrações para Azure.
ms.topic: article
ms.date: 11/20/2019
ms.openlocfilehash: 454d70a53c3ea00a4dc592703c0bc04d5517b97f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280757"
---
# <a name="discover-machine-apps-roles-and-features"></a>Descobrir aplicativos, funções e recursos de computador

Este artigo descreve como descobrir aplicativos, funções e recursos em servidores locais, usando migrações para Azure: avaliação de servidor.

Descobrir o inventário de aplicativos e funções/recursos em execução em suas máquinas locais ajuda você a identificar e planejar um caminho de migração para o Azure adaptado para suas cargas de trabalho.

> [!NOTE]
> Atualmente, o app Discovery tem suporte apenas para VMs VMware e é limitado apenas à descoberta. Ainda não oferecemos a avaliação baseada em aplicativo. Avaliação baseada em máquina para VMs VMware locais, VMs Hyper-V e servidores físicos.

Descoberta de aplicativos usando migrações para Azure: a avaliação do servidor é sem agente. Nada precisa ser instalado em máquinas e VMs. A avaliação do servidor usa o dispositivo de migrações para Azure para executar a descoberta junto com as credenciais de convidado do computador. O dispositivo acessa remotamente as máquinas VMware usando APIs do VMware.


## <a name="before-you-start"></a>Antes de começar

1. Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
2. Verifique se você [adicionou](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment a um projeto.
4. Verifique os [requisitos de VMware](migrate-support-matrix-vmware.md#vmware-requirements) para descobrir e avaliar VMs VMware com o dispositivo de migrações para Azure.
5. Verifique os [requisitos](migrate-appliance.md) para implantar o dispositivo de migrações para Azure.
6. [Verifique o suporte e os requisitos](/migrate-support-matrix-vmware.md#application-discovery) para a descoberta de aplicativos.

## <a name="prepare-for-app-discovery"></a>Preparar para a descoberta de aplicativos

1. [Prepare-se para a implantação do dispositivo](tutorial-prepare-vmware.md). A preparação inclui a verificação das configurações do dispositivo e a configuração de uma conta que será usada pelo dispositivo para acessar vCenter Server.
2. Verifique se você tem uma conta de usuário (uma para servidores Windows e Linux) com permissões de administrador para computadores nos quais você deseja descobrir aplicativos, funções e recursos.
3. [Implante o dispositivo de migrações para Azure](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o dispositivo, você baixa e importa um modelo OVA no VMware para criar o dispositivo como uma VM VMware. Você configura o dispositivo e o registra com as migrações para Azure.
2. Ao implantar o dispositivo, para iniciar a descoberta contínua, especifique o seguinte:
    - O nome do vCenter Server ao qual você deseja se conectar.
    - Credenciais que você criou para o dispositivo se conectar ao vCenter Server.
    - As credenciais de conta que você criou para o dispositivo se conectar a VMs do Windows/Linux.

Depois que o dispositivo é implantado e você forneceu credenciais, o dispositivo inicia a descoberta contínua de metadados de VM e dados de desempenho, juntamente com a descoberta de aplicativos, recursos e funções.  A duração da descoberta de aplicativos depende de quantas VMs você tem. Normalmente leva uma hora para a descoberta de aplicativos de 500 VMs.

## <a name="review-and-export-the-inventory"></a>Revisar e exportar o inventário

Após a conclusão da descoberta, se você forneceu credenciais para a descoberta de aplicativos, poderá revisar e exportar o inventário de aplicativos no portal do Azure.

1. Em **migrações para Azure-servidores** > **migrações para Azure: avaliação do servidor**, clique na contagem exibida para abrir a página **servidores descobertos** .

    > [!NOTE]
    > Nesse estágio, opcionalmente, você também pode configurar o mapeamento de dependência para computadores descobertos, para que você possa Visualizar dependências entre computadores que deseja avaliar. [Saiba mais](how-to-create-group-machine-dependencies.md).

2. Em **aplicativos descobertos**, clique na contagem exibida.
3. No **inventário de aplicativos**, você pode examinar os aplicativos, funções e recursos descobertos.
4. Para exportar o inventário, em **servidores descobertos**, clique em **Exportar inventário de aplicativos**.

O inventário de aplicativos é exportado e baixado no formato do Excel. A folha de **inventário de aplicativos** exibe todos os aplicativos descobertos em todos os computadores.

## <a name="next-steps"></a>Próximos passos

- [Crie uma avaliação](how-to-create-assessment.md) para a migração de comparação e de deslocamento dos servidores descobertos.
- Avaliar um SQL Server bancos de dados usando [migrações para Azure: avaliação de banco de dados](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
