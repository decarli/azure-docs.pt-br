---
title: Funções de modelo – lógica
description: Descreve as funções a serem usadas em um modelo do Resource Manager para determinar valores lógicos.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 48c6a05c5a449626c66f9d75c8578b217906d8e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483954"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Funções lógicas para modelos do Azure Resource Manager

O Resource Manager fornece várias funções para fazer comparações em seus modelos.

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>e a

`and(arg1, arg2, ...)`

Verifica se todos os valores de parâmetros são verdadeiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |booleano |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |booleano |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se todos os valores forem verdadeiros; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | Verdadeiro |
| notExampleOutput | Bool | Falso |

## <a name="bool"></a>bool

`bool(arg1)`

Converte o parâmetro em um booliano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um booliano. |

### <a name="return-value"></a>Valor retornado
Um booliano do valor convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) a seguir mostra como usar um booliano com uma cadeia de caracteres ou um inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| trueString | Bool | Verdadeiro |
| falseString | Bool | Falso |
| trueInt | Bool | Verdadeiro |
| falseInt | Bool | Falso |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retorna um valor com base em se uma condição é verdadeira ou falsa.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Description |
|:--- |:--- |:--- |:--- |
| condition |Sim |booleano |O valor para verificar se é verdadeiro ou falso. |
| trueValue |Sim | cadeia de caracteres, inteiro, objeto ou matriz |O valor a ser retornado quando a condição é verdadeira. |
| falseValue |Sim | cadeia de caracteres, inteiro, objeto ou matriz |O valor a ser retornado quando a condição é falsa. |

### <a name="return-value"></a>Valor retornado

Retorna o segundo parâmetro quando o primeiro parâmetro é **True**; caso contrário, retorna o terceiro parâmetro.

### <a name="remarks"></a>Comentários

Quando a condição for **verdadeira**, somente o valor verdadeiro será avaliado. Quando a condição for **falsa**, somente o valor false será avaliado. Com a função **If** , você pode incluir expressões que são apenas condicionalmente válidas. Por exemplo, você pode fazer referência a um recurso que existe sob uma condição, mas não sob a outra condição. Um exemplo de expressões de avaliação condicional é mostrado na seção a seguir.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) a seguir mostra como usar a função `if`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| yesOutput | String | sim |
| noOutput | String | não |
| objectOutput | Objeto | { "test": "value1" } |

O [modelo de exemplo](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) a seguir mostra como usar essa função com expressões que são apenas condicionalmente válidas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>não

`not(arg1)`

Converte o valor booliano em seu valor oposto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O valor a ser convertido. |

### <a name="return-value"></a>Valor retornado

Retorna **True** quando o parâmetro é **False**. Retorna **False** quando o parâmetro é **True**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | Verdadeiro |
| notExampleOutput | Bool | Falso |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir usa **not** com [equals](template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

O resultado do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Verdadeiro |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Verifica se qualquer valor do parâmetro é verdadeiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |booleano |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |booleano |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se qualquer valor for verdadeiro; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | Verdadeiro |
| notExampleOutput | Bool | Falso |

## <a name="next-steps"></a>Próximos passos

* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](template-syntax.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](create-multiple-instances.md).
* Para ver como implantar o modelo que você criou, consulte [Implantar um aplicativo com o Modelo do Azure Resource Manager](deploy-powershell.md).

