---
title: Konfigurieren von Nutzungseinstellungen in Classroom-Labs von Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Anzahl der Benutzer für das Lab konfigurieren, die Benutzer beim Lab registrieren, die Anzahl der Stunden steuern, in denen sie den virtuellen Computer verwenden können, und vieles mehr.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 834674eb63af75088434db0f614b11c7a36e7adf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964813"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurieren von Nutzungseinstellungen und Richtlinien
In diesem Artikel wird beschrieben, wie Sie dem Lab Benutzer hinzufügen, diese beim Lab registrieren, die Anzahl der Stunden steuern, für die sie den virtuellen Computer verwenden können, und vieles mehr. 


## <a name="add-users-to-the-lab"></a>Hinzufügen von Benutzern zum Lab
Wenn Sie **Zugriff beschränken** aktiviert haben, fügen Sie der Liste Benutzer (E-Mail-Adressen) hinzu.

1. Klicken Sie im linken Menü auf **Benutzer**.
2. Klicken Sie auf der Symbolleiste auf **Benutzer hinzufügen**. 

    ![Schaltfläche „Benutzer hinzufügen“](../media/how-to-configure-student-usage/add-users-button.png)
1. Geben Sie auf der Seite **Benutzer hinzufügen** die E-Mail-Adressen von Benutzern in separaten Zeilen oder durch Semikolons getrennt in einer einzelnen Zeile ein. 

    ![Hinzufügen der E-Mail-Adressen von Benutzern](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wählen Sie **Speichern** aus. In der Liste werden die E-Mail-Adressen und der Status (registriert oder nicht registriert) von Benutzern angezeigt. 

    ![Benutzerliste](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="send-registration-link-to-students"></a>Senden eines Registrierungslinks an Teilnehmer
Das folgende Verfahren enthält Schritte zum Senden eines Registrierungslinks an Benutzer. Wenn die **Zugriffsbeschränkung** für das Lab aktiviert ist, können sich nur Benutzer in der Liste der Benutzer über den Registrierungslink beim Lab registrieren. 

1. Wählen Sie im linken Menü die Option **Benutzer** aus, um zur Ansicht **Benutzer** zu wechseln. 
2. Wählen Sie die Kachel **Get registration link** (Registrierungslink abrufen) aus.

    ![Registrierungslink für Teilnehmer](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Klicken Sie im Dialogfeld **Benutzerregistrierung** auf die Schaltfläche **Kopieren**. Der Link wird in die Zwischenablage kopiert. Fügen Sie ihn in einen E-Mail-Editor ein, und senden Sie eine E-Mail an den Teilnehmer. 

    ![Registrierungslink für Teilnehmer](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Wählen Sie im Dialogfeld **Benutzerregistrierung** die Option **Schließen**. 
4. Teilen Sie den Registrierungslink mit einem Schüler bzw. Studenten, damit diese Person sich für die Klasse registrieren kann. Wenn Sie die Einstellung **Zugriff beschränken** aktiviert und der Liste Benutzer hinzugefügt haben, führen Sie die folgenden Schritte aus:
    1. Wählen Sie in der Liste die **E-Mail-Adresse** des Benutzers aus. 
    2. Sie sehen ein Fenster Ihres Standard-E-Mail-Programms, in dem das Adressfeld **An** ausgefüllt ist. 
    3. Fügen Sie die **Registrierungs-URL** ein, die Sie zuvor kopiert haben. 
    4. Senden Sie die **E-Mail**. 

## <a name="view-users-registered-with-the-lab"></a>Anzeigen der beim Lab registrierten Benutzer

Wählen Sie **Benutzer** im linken Menü, um die Liste der Benutzer anzuzeigen, die beim Lab registriert sind. 

![Liste der beim Lab registrierten Benutzer](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Festlegen von Kontingenten pro Benutzer
Mithilfe der folgenden Schritte können Sie Kontingente pro Benutzer festlegen: 

1. Klicken Sie im linken Menü auf **Benutzer**.
2. Wählen Sie auf der Symbolleiste die Option **Quota per user: unlimited** (Kontingent pro Benutzer: unbegrenzt) aus. 
3. Wählen Sie auf der Seite **Kontingent pro Benutzer** eine der folgenden Optionen aus: 
    1. **Ohne**. Benutzer können ihre virtuellen Computer nur während der geplanten Zeit, oder wenn der Lab-Besitzer virtuelle Computer für sie einschaltet, verwenden.
    2. **Unbegrenzt (Standard)**. Benutzer können ihre virtuellen Computer ohne zeitliche Einschränkung verwenden.
    3. **Anzahl von Stunden pro Benutzer angeben**. Benutzer können ihre virtuellen Computer zusätzlich zur geplanten Zeit für die festgelegte Anzahl von Stunden (siehe unten) nutzen. Wenn Sie diese Option auswählen, geben Sie die **Anzahl der Stunden** in das Textfeld ein. 

        ![Anzahl von Stunden pro Benutzer](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Wählen Sie **Speichern** aus. 
5. Jetzt sehen Sie auf der Symbolleiste die geänderten Werte: **Kontingent pro Benutzer: &lt;Anzahl Stunden&gt;**. 

    ![Kontingent pro Benutzer](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> Die geplante Ausführungszeit von virtuellen Computern wird nicht mit dem [Kontingent eines Benutzers](how-to-create-schedules.md) verrechnet. Das Kontingent dient für die Zeiten außerhalb der Stunden im Zeitplan, die ein Kursteilnehmer mit VMs verbringt. 

### <a name="add-users-by-uploading-a-csv-file"></a>Hinzufügen von Benutzern durch Hochladen einer CSV-Datei
Sie können auch eine CSV-Datei mit E-Mail-Adressen von Benutzern hochladen, um Benutzer hinzuzufügen.

1. Erstellen Sie eine CSV-Datei mit E-Mail-Adressen von Benutzern in einer Spalte.

    ![Kontingent pro Benutzer](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Wählen Sie auf der Seite **Benutzer** des Labs auf der Symbolleiste die Option **CSV hochladen** aus.

    ![Schaltfläche „CSV hochladen“](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Wählen Sie die CSV-Datei mit den Benutzer-E-Mail-Adressen aus. Wenn Sie nach Auswahl der CSV-Datei den Befehl **Öffnen** wählen, sehen Sie das folgende Fenster **Benutzer hinzufügen**. Die Liste der E-Mail-Adressen wird mit E-Mail-Adressen aus der CSV-Datei aufgefüllt. 

    ![Mit E-Mail-Adressen aus der CSV-Datei aufgefülltes Fenster „Benutzer hinzufügen“](../media/how-to-configure-student-usage/add-users-window.png)
4. Klicken Sie im Fenster **Benutzer hinzufügen** auf **Speichern**. 
5. Vergewissern Sie sich, dass in der Benutzerliste Benutzer angezeigt werden. 

    ![Liste der hinzugefügten Benutzer](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Verwalten von Benutzer-VMs
Sobald sich die Studenten bei Azure Lab Services über den von Ihnen bereitgestellten Registrierungslink registrieren, sehen Sie die den Studenten zugewiesenen VMs auf der Registerkarte **Virtuelle Computer**. 

![Studenten zugewiesene virtuelle Computer](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Sie können folgende Aufgaben auf einer Studenten-VM ausführen: 

- Beenden einer VM, wenn diese ausgeführt wird. 
- Starten einer VM, wenn diese beendet wurde. 
- Stellen Sie eine Verbindung zur VM her. 
- Löschen Sie den virtuellen Computer. 
- Zeigen Sie die Anzahl der Stunden an, für die Benutzer den virtuellen Computer verwendet haben. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Aktualisieren der Anzahl von virtuellen Computern im Lab
Um die Anzahl der virtuellen Computer im Lab zu aktualisieren, gehen Sie auf der Seite **Virtuelle Computer** folgendermaßen vor:

1. Wählen Sie im linken Menü **Virtuelle Computer** aus. 
2. Wählen Sie auf der Symbolleiste **Lab capacity (Labkapazität): &lt;Anzahl&gt; Computer** aus. 
3. Geben Sie die **Anzahl** von virtuellen Computern ein.
4. Wählen Sie **Speichern** aus.

    ![Virtuelle Computer im Lab](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln:

- [Erstellen und Verwalten von Labkonten als Administrator](how-to-manage-lab-accounts.md)
- [Erstellen und Verwalten von Labs als Labbesitzer](how-to-manage-classroom-labs.md)
- [Einrichten und Veröffentlichen von Vorlagen als Labbesitzer](how-to-create-manage-template.md)
- [Zugreifen auf ein Classroom-Lab in Azure Lab Services](how-to-use-classroom-lab.md) (als Labbenutzer)
