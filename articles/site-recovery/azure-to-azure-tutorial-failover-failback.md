---
title: Ausführen eines Failovers und Failbacks von in einer sekundären Azure-Region replizierten virtuellen Azure IaaS-Computern zur Notfallwiederherstellung mit dem Azure Site Recovery-Dienst
description: Hier erhalten Sie Informationen zum Ausführen eines Failovers und Failbacks von in einer sekundären Azure-Region replizierten virtuellen Azure IaaS-Computern zur Notfallwiederherstellung mit dem Azure Site Recovery-Dienst
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ba1e65ca915c576d2424b166488b89baa92c24a9
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729036"
---
# <a name="fail-over-and-reprotect-azure-vms-between-azure-regions"></a>Ausführen eines Failovers und erneutes Schützen von Azure-VMs zwischen Azure-Regionen

Der Dienst [Azure Site Recovery](site-recovery-overview.md) unterstützt Ihre Strategie zur Notfallwiederherstellung, indem Replikation, Failover und Failback von lokalen Computern und virtuellen Azure-Computern (Virtual Machines, VMs) verwaltet und orchestriert werden.

In diesem Tutorial wird beschrieben, wie für eine einzelne Azure-VM ein Failover zu einer sekundären Azure-Region durchgeführt wird. Nach dem Failover erfolgt ein Failback auf die primäre Region, wenn sie verfügbar ist. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Ausführen eines Failovers der Azure-VM
> * Erneutes Schützen der sekundären Azure-VM, damit sie in der primären Region repliziert wird

> [!NOTE]
> In diesem Tutorial werden für Benutzer die Schritte beschrieben, mit denen mit möglichst geringem Anpassungsaufwand ein Failover in eine Zielregion und zurück ausgeführt wird. Falls Sie mehr zu den verschiedenen Aspekten von Failovern erfahren möchten, z.B. Netzwerküberlegungen, Automatisierung oder Problembehandlung, helfen Ihnen die Gewusst wie-Dokumente für Azure-VMs weiter.

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie unbedingt ein [Notfallwiederherstellungsverfahren](azure-to-azure-tutorial-dr-drill.md) durch, um zu überprüfen, ob alles wie erwartet funktioniert.
- Überprüfen Sie die VM-Eigenschaften, bevor Sie das Testfailover ausführen. Die VM muss den [Azure-Anforderungen](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) entsprechen.

## <a name="run-a-failover-to-the-secondary-region"></a>Ausführen eines Failovers zur sekundären Region

1. Wählen Sie unter **Replizierten Elemente** die VM, für die ein Failover ausgeführt werden soll, > **Failover** aus.

   ![Failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Wählen Sie unter **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:

   * **Neuester** (Standardeinstellung): Diese Option verarbeitet alle Daten in Site Recovery und stellt die niedrigste Recovery Point Objective (RPO) bereit.
   * **Letzte Verarbeitung**: Diese Option stellt den virtuellen Computer auf den Zustand des letzten Wiederherstellungspunkts wieder her, der von Site Recovery verarbeitet wurde.
   * **Benutzerdefiniert**: Verwenden Sie diese Option für ein Failover auf einen bestimmten Wiederherstellungspunkt. Diese Option eignet sich für ein Testfailover.

3. Klicken Sie auf **Der Computer wird vor Beginn des Failovers heruntergefahren**, wenn Site Recovery versuchen soll, virtuelle Quellcomputer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Hinweis: Bei einer Sitewiederherstellung wird die Quelle nach dem Failover nicht bereinigt.

4. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.

5. Überprüfen Sie den virtuellen Computer nach Abschluss des Failovers, indem Sie sich am Computer anmelden. Falls Sie einen anderen Wiederherstellungspunkt für den virtuellen Computer verwenden möchten, können Sie die Option **Wiederherstellungspunkt ändern** wählen.

6. Nachdem Sie mit dem virtuellen Computer, für den das Failover durchgeführt wurde, zufrieden sind, können Sie für das Failover die Option **Commit** wählen.
   Dadurch werden alle mit dem Dienst verfügbaren Wiederherstellungspunkte gelöscht. Die Option **Wiederherstellungspunkt ändern** ist nicht mehr verfügbar.

## <a name="reprotect-the-secondary-vm"></a>Erneutes Schützen der sekundären VM

Nach dem Failover der VM müssen Sie sie erneut schützen, damit sie wieder zurück zur primären Region repliziert wird.

1. Stellen Sie sicher, dass sich die VM im Status **Commit für Failover ausgeführt** befindet, dass die primäre Region verfügbar ist und dass Sie neue Ressourcen darin erstellen und darauf zugreifen können.
2. Klicken Sie unter **Tresor** > **Replizierte Elemente** mit der rechten Maustaste auf die VM, für die ein Failover durchgeführt wurde, und dann auf **Erneut schützen**.

   ![Rechtsklick zum erneuten Schützen](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Beachten Sie, dass die Wirkrichtung des Schutzes – von der sekundären zur primären Region – bereits ausgewählt ist.
3. Lesen Sie die Informationen zu **Ressourcengruppe, Netzwerk, Speicher und Verfügbarkeitsgruppen**, und klicken Sie auf „OK“. Alle markierten Ressourcen (neu) werden während des Vorgangs zum erneuten Schützen erstellt.
4. Klicken Sie auf **OK**, um einen Auftrag zum erneuten Schützen auszulösen. Dieser Auftrag liefert die neuesten Daten an den Zielstandort. Anschließend werden die Deltas zur primären Region repliziert. Die VM befindet sich jetzt in einem geschützten Zustand.

> [!NOTE]
> Weitere Informationen zum Workflow für den erneuten Schutz und dazu, was während des erneuten Schutzes passiert, finden Sie unter [Erneutes Schützen von virtuellen Azure-Computern, für die ein Failover zur primären Region durchgeführt wurde](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).


## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie [Durchführen eines Failbacks](azure-to-azure-tutorial-failback.md).
