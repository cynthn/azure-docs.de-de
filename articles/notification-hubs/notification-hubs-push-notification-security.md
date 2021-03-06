---
title: Sicherheit von Notification Hubs
description: In diesem Thema wird Sicherheit für Azure Notification Hubs erläutert.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bd9df12cbe941b868c769daccd02c1d81b39f7bd
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465359"
---
# <a name="security-model-of-azure-notification-hubs"></a>Sicherheitsmodell von Azure Notification Hubs

## <a name="overview"></a>Übersicht

Dieses Thema beschreibt das Sicherheitsmodell von Azure Notification Hubs. Da Notification Hubs eine Service Bus-Entität sind, wird das gleiche Sicherheitsmodell wie bei Service Bus implementiert. Weitere Informationen finden Sie unter [Service Bus Authentication](https://msdn.microsoft.com/library/azure/dn155925.aspx) (in englischer Sprache).

## <a name="shared-access-signature-security-sas"></a>Shared Access Signature-Sicherheit (SAS)

Notification Hubs implementieren ein Sicherheitsschema auf Entitätsebene, das SAS (Shared Access Signature) genannt wird. Dieses Schema ermöglicht Messagingentitäten das Deklarieren von bis zu 12 Autorisierungsregeln in ihrer Beschreibung, die Zugriffsrechte für diese Entität gewähren.

Jede Regel enthält einen Namen, einen Schlüsselwert (gemeinsamer geheimer Schlüssel) und eine Reihe von Rechten, wie im Abschnitt „Sicherheitsansprüche“ erläutert. Beim Erstellen eines Notification Hubs werden automatisch zwei Regeln erstellt: eine mit Lauschberechtigung (von der Clientanwendung verwendet) und eine mit allen Berechtigungen (vom Back-End verwendet).

Wenn eine Registrierungsverwaltung von Client-Apps aus durchgeführt wird und die über Benachrichtigungen gesendeten Informationen nicht vertraulich sind (z. B. aktuelle Wetterdaten), besteht eine gebräuchliche Möglichkeit zum Zugreifen auf einen Notification Hub darin, dem Schlüsselwert der Regel nur Lauschzugriff für die Client-App zu erteilen und dem Schlüsselwert der Regel Vollzugriff auf das Back-End zu gewähren.

Es wird nicht empfohlen, den Schlüsselwert in Windows Store-Client-Apps einzubetten. Eine Möglichkeit, das Einbetten des Schlüsselwerts zu vermeiden, besteht darin, ihm beim Start der Client-App durch diese aus dem App-Back-End abrufen zu lassen.

Beachten Sie unbedingt, dass der Schlüssel mit Lauschzugriff einer Clientanwendung die Möglichkeit gibt, sich für jedes beliebige Tag zu registrieren. Wenn Ihre App Registrierungen auf bestimmte Tags für bestimmte Clients einschränken muss (z. B., wenn Tags für Benutzer-IDs stehen), muss das App-Back-End die Registrierungen ausführen. Weitere Informationen finden Sie unter „Registrierungsverwaltung“. Beachten Sie, dass die Clientanwendung auf diese Weise keinen direkten Zugriff auf Notification Hubs hat.

## <a name="security-claims"></a>Sicherheitsansprüche

Ähnlich wie bei anderen Entitäten sind Notification Hub-Vorgänge für drei Sicherheitsansprüche zulässig: Lauschen, Senden und Verwalten.

| Anspruch   | BESCHREIBUNG                                          | Zulässige Vorgänge |
| ------- | ---------------------------------------------------- | ------------------ |
| Lauschen  | Erstellen/Aktualisieren, Lesen und Löschen einzelner Registrierungen | Erstellen/Aktualisieren einer Registrierung<br><br>Lesen der Registrierung<br><br>Lesen aller Registrierungen für ein Handle<br><br>Löschen einer Registrierung |
| Send    | Senden von Nachrichten an den Notification Hub                | Nachricht senden |
| Verwalten  | CRUDs für Notification Hubs (einschließlich des Aktualisierens von PNS-Anmeldeinformationen und Sicherheitsschlüsseln) und Lesen von Registrierungen basierend auf Tags |Erstellen/Aktualisieren/Lesen/Löschen von Notification Hubs<br><br>Lesen von Registrierungen nach Tag |

Notification Hubs akzeptieren Ansprüche, die durch Microsoft Azure Access Control-Token und von Signaturtoken mit freigegebenen Schlüsseln, die direkt auf dem Notification Hub konfiguriert wurden, erteilt werden.
