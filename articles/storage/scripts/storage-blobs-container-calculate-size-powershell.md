---
title: Azure PowerShell-Skriptbeispiel – Berechnen der Größe des Blobcontainers | Microsoft-Dokumentation
description: Berechnen Sie die Größe eines Containers in Azure Blob Storage, indem Sie die Größe der einzelnen Blobs addieren.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: d8baec875c25556f1080cdd105c7fa466ffce74e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094006"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Berechnen der Größe eines Blob Storage-Containers

Mit diesem Skript berechnen Sie die Größe eines Containers in Azure Blob Storage, indem Sie die Größe der Blobs im Container addieren.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Dieses PowerShell-Skript gibt eine geschätzte Größe für den Container an und sollte nicht für Abrechnungsberechnungen verwendet werden. Ein Skript, mit dem die Containergröße zu Abrechnungszwecken berechnet wird, finden Sie unter [Berechnen der Größe eines Blob Storage-Containers zu Abrechnungszwecken](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den Container und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle, um die Größe des Blob Storage-Containers zu berechnen. Jedes Element in der Tabelle ist mit der Dokumentation des jeweiligen Befehls verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Ruft ein angegebenes Speicherkonto oder alle Speicherkonten in einer Ressourcengruppe oder im Abonnement ab. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Listet die Blobs in einem Container auf. |

## <a name="next-steps"></a>Nächste Schritte

Ein Skript, mit dem die Containergröße zu Abrechnungszwecken berechnet wird, finden Sie unter [Berechnen der Größe eines Blob Storage-Containers zu Abrechnungszwecken](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Weitere PowerShell-Skriptbeispiele für Speicher finden Sie in den [PowerShell-Beispielen für Azure Storage](../blobs/storage-samples-blobs-powershell.md).
