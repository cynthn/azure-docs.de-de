---
title: 'Tutorial: Bereitstellen einer Azure-Funktion auf einem Gerät – Azure IoT Edge | Microsoft-Dokumentation'
description: In diesem Tutorial entwickeln Sie eine Azure-Funktion als IoT Edge-Modul und stellen sie anschließend auf einem Edgegerät bereit.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1fba2c4e5191d4c827035362a8eb6876fcbb67cc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58081745"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Bereitstellen von Azure-Funktionen als IoT Edge-Module

Mithilfe von Azure Functions können Sie Code bereitstellen, mit dem Ihre Geschäftslogik direkt auf Ihren Azure IoT Edge-Geräten implementiert wird. In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie eine Azure-Funktion erstellen und bereitstellen, die Sensordaten auf dem simulierten IoT Edge-Gerät filtert. Sie verwenden das simulierte IoT Edge-Gerät, das Sie in den Schnellstartanleitungen zum Bereitstellen von Azure IoT Edge auf einem simulierten Gerät unter [Windows](quickstart.md) oder [Linux](quickstart-linux.md) erstellt haben. In diesem Tutorial lernen Sie Folgendes:     

> [!div class="checklist"]
> * Erstellen einer Azure-Funktion mithilfe von Visual Studio Code
> * Erstellen und Veröffentlichen eines Docker-Images in einer Containerregistrierung mithilfe von VS Code und Docker
> * Bereitstellen des Moduls über die Containerregistrierung auf Ihrem IoT Edge-Gerät
> * Anzeigen gefilterter Daten

<center>

![Architekturdiagramm des Tutorials: Staging und Bereitstellung des Funktionsmoduls](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Azure Function-Module in Azure IoT Edge sind als [öffentliche Vorschauversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verfügbar. 

Die Azure-Funktion, die Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Die Funktion sendet Nachrichten nur dann an Azure IoT Hub (Upstream), wenn die Temperatur einen angegebenen Schwellenwert überschreitet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure IoT Edge-Gerät:

* Sie können Ihren Entwicklungscomputer oder eine VM als IoT Edge-Gerät einrichten, indem Sie die Schritte im Schnellstart für [Linux](quickstart-linux.md)- oder [Windows](quickstart.md)-Geräte ausführen.

Cloudressourcen:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“. 

Entwicklungsressourcen:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Erweiterung C# for Visual Studio Code mit Unterstützung von OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Azure IoT-Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

In diesem Tutorial verwenden Sie die Azure IoT-Tools für Visual Studio Code zum Entwickeln eines Moduls und Erstellen eines **Containerimages** aus den Dateien. Danach pushen Sie dieses Image in ein **Repository**, in dem Ihre Images gespeichert und verwaltet werden. Abschließend stellen Sie Ihr Image aus der Registrierung zur Ausführung auf dem IoT Edge-Gerät bereit.  

Sie können eine beliebige Docker-kompatible Registrierung zum Speichern Ihrer Containerimages verwenden. Zwei beliebte Docker-Registrierungsdienste sind [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) und [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). In diesem Tutorial wird Azure Container Registry verwendet. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Optionen **Ressource erstellen** > **Container** > **Container Registry** aus.

    ![Erstellen einer Containerregistrierung im Azure-Portal](./media/tutorial-deploy-function/create-container-registry.png)

2. Geben Sie die folgenden Werte an, um Ihre Containerregistrierung zu erstellen:

   | Feld | Wert | 
   | ----- | ----- |
   | Registrierungsname | Geben Sie einen eindeutigen Namen an. |
   | Abonnement | Wählen Sie ein Abonnement aus der Dropdownliste aus. |
   | Ressourcengruppe | Es wird empfohlen, die gleiche Ressourcengruppe für alle Testressourcen zu verwenden, die Sie während der IoT Edge-Schnellstarts und -Tutorials erstellen. Beispielsweise **IoTEdgeResources**. |
   | Standort | Wählen Sie einen Standort in Ihrer Nähe aus. |
   | Administratorbenutzer | Legen Sie **Aktivieren** fest. |
   | SKU | Wählen Sie **Basic** aus. | 

5. Klicken Sie auf **Erstellen**.

6. Navigieren Sie nach der Erstellung der Containerregistrierung zu dieser Registrierung, und klicken Sie anschließend auf **Zugriffsschlüssel**. 

7. Kopieren Sie die Werte für **Anmeldeserver**, **Benutzername** und **Kennwort**. Sie verwenden diese Werte an späterer Stelle des Tutorials, um Zugriff auf die Containerregistrierung zu gewähren. 

## <a name="create-a-function-project"></a>Erstellen eines Funktionsprojekts

Die Azure IoT-Tools für Visual Studio Code, die Sie in den Voraussetzungen installiert haben, bietet Verwaltungsfunktionen sowie einige Codevorlagen. In diesem Abschnitt verwenden Sie Visual Studio Code zum Erstellen einer IoT Edge-Projektmappe, die eine Azure-Funktion enthält. 

1. Öffnen Sie Visual Studio Code auf Ihrem Entwicklungscomputer.

2. Öffnen Sie die VS Code-Befehlspalette, indem Sie auf **Ansicht** > **Befehlspalette** klicken.

3. Geben Sie in der Befehlspalette den Befehl **Azure IoT Edge: New IoT Edge Solution** ein, und führen Sie ihn aus. Folgen Sie den Anweisungen in der Befehlspalette, um Ihre Projektmappe zu erstellen.

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein (beispielsweise **FunctionSolution**), oder übernehmen Sie den Standardnamen. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Azure Functions – C#** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **CSharpFunction**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Ihr Containerimage wird aus dem vorherigen Schritt übernommen. Ersetzen Sie **localhost:5000** durch den Anmeldeserverwert aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. Die endgültige Zeichenfolge sieht wie folgt aus: „\<Registrierungsname\>.azurecr.io/CSharpFunction“. |

   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-deploy-function/repository.png)

4. Im VS Code-Fenster wird der Arbeitsbereich Ihrer IoT Edge-Projektmappe geladen: ein Ordner vom Typ „\.vscode“, ein Modulordner und eine Vorlagendatei für das Bereitstellungsmanifest und eine Datei vom Typ „\.env“. Öffnen Sie im VS Code-Explorer **Module** > **CSharpFunction** > **CSharpFunction.cs**.

5. Ersetzen Sie den Inhalt der Datei **CSharpFunction.cs** durch den folgenden Code. Dieser Code empfängt Telemetriedaten zur Umgebungs- und Gerätetemperatur und leitet die Nachricht nur dann an IoT Hub weiter, wenn die Gerätetemperatur über einem festgelegten Schwellenwert liegt.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}         
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}         
       }
   }
   ```

6. Speichern Sie die Datei .

## <a name="build-your-iot-edge-solution"></a>Erstellen Ihrer IoT Edge-Projektmappe

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und **CSharpFunction** Code hinzugefügt, um Nachrichten herauszufiltern, deren gemeldete Computertemperatur unter dem zulässigen Schwellenwert liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

In diesem Abschnitt geben Sie die Anmeldeinformationen für Ihre Containerregistrierung zweimal an. Das erste Mal, um sich lokal auf Ihrem Entwicklungscomputer anzumelden, damit Visual Studio Code per Push Images an Ihre Registrierung übertragen kann. Das zweite Mal in der **ENV**-Datei Ihrer IoT Edge-Projektmappe, damit Ihr IoT Edge Gerät Berechtigungen zum Abrufen von Images per Pull aus Ihrer Registrierung erhält. 

1. Öffnen Sie das in VS Code integrierte Terminal über **Ansicht** > **Terminal**. 

2. Melden Sie sich bei Ihrer Containerregistrierung an, indem Sie den folgenden Code im integrierten Terminal eingeben. Verwenden Sie den Benutzernamen und den Anmeldeserver, die Sie zuvor aus Ihrer Azure-Containerregistrierung kopiert haben.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Wenn Sie zur Eingabe des Kennworts aufgefordert werden, fügen Sie das Kennwort für Ihre Containerregistrierung ein, und drücken Sie die **EINGABETASTE**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. Öffnen Sie im VS Code-Explorer die Datei **deployment.template.json** im Arbeitsbereich für Ihre IoT Edge-Projektmappe. Diese Datei teilt der IoT Edge-Runtime mit, welche Module auf einem Gerät bereitgestellt werden sollen. Beachten Sie, dass Ihr Function-Modul **CSharpFunction** zusammen mit dem Modul **tempSensor** aufgeführt wird, das Testdaten bereitstellt. Weitere Informationen zu Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können – Vorschau](module-composition.md).

   ![Anzeigen Ihres Moduls im Bereitstellungsmanifest](./media/tutorial-deploy-function/deployment-template.png)

3. Öffnen Sie die **ENV**-Datei im Arbeitsbereich Ihrer IoT Edge-Projektmappe. Diese von Git ignorierte Datei speichert die Anmeldeinformationen für Ihre Containerregistrierung, damit Sie sie nicht in die Vorlage für das Bereitstellungsmanifest aufnehmen müssen. Geben Sie den **Benutzernamen** und das **Kennwort** für Ihre Containerregistrierung an. 

5. Speichern Sie diese Datei.

6. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei „deployment.template.json“, und wählen Sie anschließend **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und übertragen) aus. 

Wenn Sie Visual Studio Code anweisen, die Projektmappe zu erstellen, wird zunächst basierend auf den Informationen in der Bereitstellungsvorlage eine Datei vom Typ „deployment.json“ in einem neuen Ordner namens **config** erstellt. Anschließend werden zwei Befehle im integrierten Terminal ausgeführt: `docker build` und `docker push`. Diese beiden Befehle erstellen Ihren Code, packen die Funktionen in Container und pushen den Code anschließend an die Containerregistrierung, die Sie beim Initialisieren der Projektmappe angegeben haben. 

## <a name="view-your-container-image"></a>Anzeigen des Containerimages

Visual Studio Code gibt eine Erfolgsmeldung aus, wenn das Containerimage an die Containerregistrierung übertragen wurde. Wenn Sie selbst überprüfen möchten, ob die Ausführung erfolgreich war, können Sie das Image in der Registrierung anzeigen. 

1. Navigieren Sie im Azure-Portal zu Ihrer Azure-Containerregistrierung. 
2. Wählen Sie **Repositorys** aus.
3. Die Liste sollte das Repository **csharpfunction** enthalten. Wählen Sie dieses Repository aus, um weitere Details anzuzeigen.
4. Im Abschnitt **Tags** sollte das Tag **0.0.1-amd64** angezeigt werden. Dieses Tag gibt die Version und die Plattform des erstellten Images an. Diese Werte sind in der Datei „module.json“ im Ordner „CSharpFunction“ festgelegt. 

## <a name="deploy-and-run-the-solution"></a>Bereitstellen und Ausführen der Projektmappe

Sie können das Azure-Portal verwenden, um Ihr Funktionsmodul wie in den Schnellstartanleitungen auf einem IoT Edge-Gerät bereitzustellen. Sie können Module aber auch über Visual Studio Code bereitstellen und überwachen. In den folgenden Abschnitten werden die Azure IoT-Tools für VS Code verwendet, die als erforderliche Komponente aufgeführt sind. Sofern Sie die Erweiterung noch nicht installiert haben, holen Sie dies jetzt nach. 

1. Öffnen Sie die VS Code-Befehlspalette, indem Sie auf **Ansicht** > **Befehlspalette** klicken.

2. Suchen Sie den Befehl **Azure: Sign in** (Azure: Anmelden), und führen Sie ihn aus. Befolgen Sie die Anweisungen zum Anmelden bei Ihrem Azure-Konto. 

3. Suchen Sie in der Befehlspalette nach dem Befehl **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub auswählen), und führen Sie ihn aus. 

4. Wählen Sie das Abonnement mit Ihrer IoT Hub-Instanz und anschließend die gewünschte IoT Hub-Instanz aus.

5. Erweitern Sie im VS Code-Explorer den Abschnitt **Azure IoT Hub-Geräte**. 

6. Klicken Sie mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und klicken Sie dann auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen). 

7. Navigieren Sie zum Projektmappenordner mit **CSharpFunction**. Öffnen Sie den Konfigurationsordner, wählen Sie die Datei **deployment.json** aus, und klicken Sie anschließend auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen).

8. Aktualisieren Sie den Abschnitt **Azure IoT Hub-Geräte**. Nun sollte das neue **CSharpFunction**-Modul zusammen mit dem **TempSensor**-Modul sowie **$edgeAgent** und **$edgeHub** ausgeführt werden. Es kann etwas dauern, bis die neuen Module angezeigt werden. Ihr IoT Edge-Gerät muss die neuen Bereitstellungsinformationen von IoT Hub abrufen, die neuen Container starten und dann den Status an IoT Hub zurückmelden. 

   ![Anzeigen der bereitgestellten Module in VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Anzeigen generierter Daten

Sie können alle bei der IoT Hub-Instanz eingehenden Nachrichten anzeigen, indem Sie in der Befehlspalette **Azure IoT Hub: Start Monitoring D2C Message** (Azure IoT Hub: Überwachung von D2C-Nachrichten starten) ausführen.

Sie können die Ansicht auch filtern, um alle Nachrichten anzuzeigen, die von einem bestimmten Gerät an Ihre IoT Hub-Instanz gesendet werden. Klicken Sie im Abschnitt **Azure IoT Hub-Geräte** mit der rechten Maustaste auf das Gerät, und klicken Sie auf **Start Monitoring D2C Messages** (Überwachung von D2C-Nachrichten starten).

Wenn Sie die Nachrichtenüberwachung beenden möchten, führen Sie in der Befehlspalette den Befehl **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Überwachung von D2C-Nachrichten beenden) aus. 


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen. 

Andernfalls können Sie die in diesem Artikel erstellten lokalen Konfigurationen und Azure-Ressourcen löschen, um Kosten zu vermeiden. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Azure-Funktion mit Code zum Filtern von Rohdaten erstellt, die von Ihrem IoT Edge-Gerät generiert werden. Wenn Sie eigene Module erstellen möchten, finden Sie entsprechende Informationen unter [Verwenden von Visual Studio Code zum Entwickeln und Debuggen von Azure Functions-Funktionen für Azure IoT Edge](how-to-develop-csharp-function.md). 

In den nächsten Tutorials erfahren Sie, wie Ihnen Azure IoT Edge noch dabei helfen kann, Daten in geschäftliche Erkenntnisse auf Edge-Ebene zu verwandeln.

> [!div class="nextstepaction"]
> [Bereitstellen von Azure Stream Analytics als IoT Edge-Modul (Vorschauversion)](tutorial-deploy-stream-analytics.md)

