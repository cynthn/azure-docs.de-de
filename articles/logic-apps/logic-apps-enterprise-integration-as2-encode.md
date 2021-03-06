---
title: Codieren von AS2-Nachrichten – Azure Logic Apps | Microsoft-Dokumentation
description: Codieren von AS-Nachrichten mit Azure Logic Apps und Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.date: 08/08/2018
ms.openlocfilehash: 6bb19199929a004ee5668a3a6e057a69c24dd752
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122712"
---
# <a name="encode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Codieren von AS-Nachrichten mit Azure Logic Apps und Enterprise Integration Pack

Verwenden Sie den Connector zum Codieren von AS2-Nachrichten, um eine sichere und zuverlässige Nachrichtenübertragung zu gewährleisten. Dieser Connector ermöglicht die digitale Signierung, Verschlüsselung und Bestätigungen durch Benachrichtigungen über den Nachrichtenstatus (Message Disposition Notifications, MDNs), was in einer Unterstützung der Nichtabstreitbarkeit resultiert.

## <a name="before-you-start"></a>Vorbereitung

Sie benötigen Folgendes:

* Ein Azure-Konto. Sie können ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Ein bereits definiertes und mit Ihrem Azure-Abonnement verknüpftes [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md). Für die Verwendung des Connectors zum Codieren von AS2-Nachrichten ist ein Integrationskonto erforderlich.
* Mindestens zwei [Partner](logic-apps-enterprise-integration-partners.md), die bereits in Ihrem Integrationskonto definiert sind.
* Eine bereits in Ihrem Integrationskonto definierte [AS2-Vereinbarung](logic-apps-enterprise-integration-as2.md).

## <a name="encode-as2-messages"></a>Codieren von AS2-Nachrichten

1. [Erstellen Sie eine Logik-App](quickstart-create-first-logic-app-workflow.md).

2. Da der Connector zum Codieren von AS2-Nachrichten über keine Trigger verfügt, müssen Sie einen Trigger zum Starten Ihrer Logik-App hinzufügen (beispielsweise einen Anforderungstrigger). Fügen Sie im Logik-App-Designer einen Trigger hinzu, und fügen Sie anschließend Ihrer Logik-App eine Aktion hinzu.

3.  Geben Sie „AS2“ als Filter in das Suchfeld ein. Wählen Sie **AS2 - Encode AS2 message** (AS2 – AS2-Nachricht codieren) aus.
   
    ![Nach „AS2“ suchen](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Falls Sie noch keine Verbindungen mit Ihrem Integrationskonto erstellt haben, werden Sie aufgefordert, eine solche Verbindung zu erstellen. Benennen Sie die Verbindung, und wählen Sie das Integrationskonto aus, mit dem Sie eine Verbindung herstellen möchten. 
   
    ![Integrationskontoverbindung erstellen](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Mit einem Sternchen gekennzeichnete Eigenschaften müssen angegeben werden.

    | Eigenschaft | Details |
    | --- | --- |
    | Verbindungsname* |Geben Sie einen beliebigen Namen für Ihre Verbindung ein. |
    | Integrationskonto* |Geben Sie einen Namen für Ihr Integrationskonto ein. Achten Sie darauf, dass sich Ihr Integrationskonto und Ihre Logik-App am gleichen Azure-Standort befinden. |

5.  Am Ende sollten Ihre Verbindungsdetails in etwa wie im folgenden Beispiel aussehen. Wählen Sie **Erstellen** aus, um die Verbindungserstellung abzuschließen.
   
    ![Details zur Integrationsverbindung](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Geben Sie nach der Verbindungserstellung wie in diesem Beispiel dargestellt Details für **AS2-From** und **AS2-To** (gemäß Konfiguration in der Vereinbarung) sowie **Text** (die Nachrichtennutzlast) ein.
   
    ![Pflichtfelder ausfüllen](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Details des AS2-Encoders

Der Connector zum Codieren von AS2-Nachrichten führt folgende Aufgaben aus: 

* Anwenden von AS2-/HTTP-Headern
* Signieren ausgehender Nachrichten (sofern konfiguriert)
* Verschlüsseln ausgehender Nachrichten (sofern konfiguriert)
* Komprimieren der Nachricht(sofern konfiguriert)
* Übertragen des Dateinamens in MIME-Kopfzeile (sofern konfiguriert)


  > [!NOTE]
  > Wenn Sie Azure Key Vault für die Zertifikatsverwaltung verwenden, achten Sie darauf, die Schlüssel so zu konfigurieren, dass der Vorgang **Verschlüsseln** zulässig ist.
  > Andernfalls schlägt die AS2-Codierung fehl.
  >
  > ![KeyVault-Entschlüsselungen](media/logic-apps-enterprise-integration-as2-encode/keyvault1.png)

## <a name="try-this-sample"></a>Beispiel zum Ausprobieren

Wenn Sie eine uneingeschränkt funktionsfähige Logik-App und ein AS2-Beispielszenario bereitstellen möchten, sehen Sie sich [Azure Logic Apps - AS2 Send Receive](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) (Azure Logic Apps – AS2: Senden/Empfangen) an.

## <a name="view-the-swagger"></a>Anzeigen von Swagger
Weitere Informationen finden Sie unter [Details zu Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zum Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack") 

