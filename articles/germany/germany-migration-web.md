---
title: Migrieren von Azure-Webressourcen von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Webressourcen von Azure Deutschland zu Azure weltweit.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: d1bfce9b58f6b0cd681b6b4dac70c925084db193
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599766"
---
# <a name="migrate-web-resources-to-global-azure"></a>Migrieren von Webressourcen zu Azure weltweit

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Webressourcen von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="web-apps"></a>Web-Apps

Migrieren von Apps, die Sie mit dem Web Apps-Feature von Azure App Service erstellt haben, von Azure Deutschland in Azure weltweit wird derzeit nicht unterstützt. Es empfiehlt sich, dass Sie eine Web-App als Azure Resource Manager-Vorlage exportieren. Danach können Sie die App erneut bereitstellen, nachdem Sie die Eigenschaft für den Standort auf die neue Zielregion geändert haben.

> [!IMPORTANT]
> Ändern Sie den Standort, Azure Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu App Service](https://docs.microsoft.com/azure/app-service/#step-by-step-tutorials) durcharbeiten.
- Informieren Sie sich darüber, wie eine [Azure Resource Manager-Vorlage exportiert wird](../azure-resource-manager/resource-manager-export-template.md).
- Erfahren Sie, wie Sie [eine Azure Resource Manager-Vorlage über PowerShell exportieren](../azure-resource-manager/resource-manager-export-template-powershell.md#export-resource-group-as-template).
- Lesen Sie die [Übersicht über Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Lesen Sie die [Übersicht über Azure App Service](../app-service/overview.md).
- Verschaffen Sie sich einen [Überblick über Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
- Erfahren Sie, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="notification-hubs"></a>Notification Hubs

Um Einstellungen von einer Azure Notification Hubs-Instanz zu einer anderen Instanz zu migrieren, exportieren und importieren Sie alle Registrierungstoken samt deren Tags:

1. [Exportieren Sie die vorhandenen Notification Hub-Registrierungen](https://msdn.microsoft.com/library/azure/dn790624.aspx) in einen Azure Blob Storage-Container.
1. Erstellen Sie einen neuen Notification Hub in der Zielumgebung.
1. [Importieren Sie Ihre Registrierungstoken](https://msdn.microsoft.com/library/azure/dn790624.aspx) aus Blobspeicher in Ihren neuen Notification Hub.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Notification Hubs-Tutorials](https://docs.microsoft.com/azure/notification-hubs/#step-by-step-tutorials) durcharbeiten.
- Lesen Sie die [Übersicht über Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Speicher](./germany-migration-storage.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Sicherheit](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)
