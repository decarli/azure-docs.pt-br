---
title: Entender o suporte do MQTT do serviço de provisionamento de dispositivos IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor-suporte para dispositivos que se conectam ao ponto de extremidade voltado para o dispositivo do DPS (serviço de provisionamento de dispositivos) do Azure IoT usando o protocolo MQTT.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: ea6ece7e34ddb9c25f9f8349239ab3a1c3405abf
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973366"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Comunicar-se com seu DPS usando o protocolo MQTT

O DPS permite que os dispositivos se comuniquem com o ponto de extremidade do dispositivo DPS usando:

* [MQTT v 3.1.1](https://mqtt.org/) na porta 8883
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) sobre WebSocket na porta 443.

O DPS não é um agente MQTT completo e não dá suporte a todos os comportamentos especificados no MQTT v 3.1.1 Standard. Este artigo descreve como os dispositivos podem usar os comportamentos de MQTT com suporte para se comunicar com o DPS.

Todas as comunicações de dispositivo com o DPS devem ser protegidas usando TLS/SSL. Portanto, o DPS não dá suporte a conexões não seguras pela porta 1883.

 > [!NOTE] 
 > No momento, o DPS não dá suporte a dispositivos que usam o [mecanismo de atestado](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) do TPM sobre o protocolo MQTT.

## <a name="connecting-to-dps"></a>Conectando-se ao DPS

Um dispositivo pode usar o protocolo MQTT para se conectar a um DPS usando qualquer uma das opções a seguir.

* Bibliotecas nos [SDKs de provisionamento do Azure IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* O protocolo MQTT diretamente.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Usando o protocolo MQTT diretamente (como um dispositivo)

Se um dispositivo não puder usar os SDKs do dispositivo, ele poderá se conectar com os pontos de extremidade públicos do dispositivo usando o protocolo MQTT na porta 8883. No pacote do **Connect** , o dispositivo deve usar os seguintes valores:

* Para o campo **ClientID** , use **RegistrationId**.

* Para o campo **username** , use `{idScope}/registrations/{registration_id}/api-version=2019-03-31`, em que `{idScope}` é o [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) do DPS.

* No campo **Senha** use um token SAS. O formato do token SAS é o mesmo, conforme descrito para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` o ResourceURI deve estar no formato `{idScope}/registrations/{registration_id}`. O nome da política deve ser `registration`.

  > [!NOTE]
  > Se você usa a autenticação de certificado X.509, as senhas de token SAS não são necessárias.

  Para obter mais informações sobre como gerar tokens SAS, consulte a seção tokens de segurança de [controlar o acesso ao DPS](how-to-control-access.md#security-tokens).

Veja a seguir uma lista de comportamentos específicos de implementação de DPS:

 * O DPS não oferece suporte à funcionalidade do sinalizador **CleanSession** que está sendo definido como **0**.

 * Quando um aplicativo de dispositivo assina um tópico com o **QoS 2**, o DPS concede o nível 1 de QoS máximo no pacote **SUBACK** . Depois disso, o DPS entrega mensagens para o dispositivo usando a QoS 1.

## <a name="tlsssl-configuration"></a>Configuração de TLS/SSL

Para usar o protocolo MQTT diretamente, o cliente *deve* se conectar via TLS 1,2. Tentativas de ignorar essa etapa falham com erros de conexão.


## <a name="registering-a-device"></a>Registrando um dispositivo

Para registrar um dispositivo por meio do DPS, um dispositivo deve assinar usando `$dps/registrations/res/#` como um **filtro de tópico**. O curinga de vários níveis `#` no filtro de tópico é usado apenas para permitir que o dispositivo receba propriedades adicionais no nome do tópico. O DPS não permite o uso dos curingas `#` ou `?` para filtragem de subtópicos. Como o DPS não é um agente de mensagens pub-sub de uso geral, ele dá suporte apenas aos nomes de tópico e filtros de tópico documentados.

O dispositivo deve publicar uma mensagem de registro no DPS usando `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` como um **nome de tópico**. A carga deve conter o objeto de [registro do dispositivo](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) no formato JSON.
Em um cenário bem-sucedido, o dispositivo receberá uma resposta no nome do tópico de `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` em que x é o valor de repetição-após, em segundos. A carga da resposta conterá o objeto [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) no formato JSON.

## <a name="polling-for-registration-operation-status"></a>Sondando o status da operação de registro

O dispositivo deve sondar o serviço periodicamente para receber o resultado da operação de registro do dispositivo. Supondo que o dispositivo já tenha se inscrito no tópico `$dps/registrations/res/#`, conforme indicado acima, ele pode publicar uma mensagem Get OperationStatus para o nome do tópico `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}`. A ID da operação nesta mensagem deve ser o valor recebido na mensagem de resposta RegistrationOperationStatus na etapa anterior. No caso bem-sucedido, o serviço responderá no tópico `$dps/registrations/res/200/?$rid={request_id}`. A carga da resposta conterá o objeto RegistrationOperationStatus. O dispositivo deve continuar a sondar o serviço se o código de resposta for 202 após um atraso igual ao período de repetição-após. A operação de registro do dispositivo será bem-sucedida se o serviço retornar um código de status 200.

## <a name="connecting-over-websocket"></a>Conectando por WebSocket
Ao conectar-se via WebSocket, especifique o subprotocolo como `mqtt`. Siga o [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o protocolo MQTT, consulte a [documentação do MQTT](https://mqtt.org/documentation).

Para explorar ainda mais os recursos do DPS, consulte:

* [Sobre o DPS de IoT](about-iot-dps.md)
