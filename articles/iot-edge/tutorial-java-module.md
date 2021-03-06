---
title: 'Tutorial: Erstellen eines benutzerdefinierten Java-Moduls – Azure IoT Edge | Microsoft-Dokumentation'
description: In diesem Tutorial wird gezeigt, wie Sie ein IoT Edge-Modul mit Java-Code erstellen und es auf einem Edge-Gerät bereitstellen.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 9a541f42670b3ccf83331e3e2e9069289bb9b4b3
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224072"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutorial: Entwickeln und Bereitstellen eines Java-IoT Edge-Moduls für Ihr simuliertes Gerät

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, der Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert. In diesem Tutorial wird beschrieben, wie Sie ein IoT Edge-Modul, mit dem Sensordaten gefiltert werden, erstellen und bereitstellen. Sie verwenden das simulierte IoT Edge-Gerät, das Sie in den Schnellstartanleitungen zum Bereitstellen von Azure IoT Edge auf einem simulierten Gerät unter [Windows](quickstart.md) oder [Linux](quickstart-linux.md) erstellt haben. In diesem Tutorial lernen Sie Folgendes:    

> [!div class="checklist"]
> * Erstellen eines IoT Edge-Java-Moduls mithilfe von Visual Studio Code auf der Grundlage des Azure IoT Edge-Maven-Vorlagenpakets und des Azure IoT-Java-Geräte-SDK.
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio Code und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten


Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Es leitet Nachrichten nur dann an nachgeschaltete Geräte weiter, wenn die Temperatur einen angegebenen Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Voraussetzungen

Ein Azure IoT Edge-Gerät:

* Sie können ein IoT Edge-Gerät einrichten, indem Sie die Schritte in den Schnellstartanleitungen für [Linux](quickstart-linux.md) bzw. [Windows](quickstart.md) ausführen.
* Bei IoT Edge auf Windows-Geräten unterstützt Version 1.0.5 keine Java-Module. Weitere Informationen finden Sie in den [Versionshinweisen zur Version 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Die Schritte zum Installieren einer bestimmten Version finden Sie unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md).

Cloudressourcen:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“. 

Entwicklungsressourcen:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Java-Erweiterungspaket](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) für Visual Studio Code
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) für Visual Studio Code 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) und [Festlegen der `JAVA_HOME`-Umgebungsvariable](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) für den Verweis auf Ihre JDK-Installation
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * Stellen Sie bei der Entwicklung auf einem Windows-Gerät sicher, dass Docker [zur Verwendung von Linux-Containern konfiguriert ist](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

In diesem Tutorial verwenden Sie die Azure IoT-Tools für Visual Studio Code zum Entwickeln eines Moduls und Erstellen eines **Containerimages** aus den Dateien. Danach pushen Sie dieses Image in ein **Repository**, in dem Ihre Images gespeichert und verwaltet werden. Abschließend stellen Sie Ihr Image aus der Registrierung zur Ausführung auf dem IoT Edge-Gerät bereit.  

Sie können eine beliebige Docker-kompatible Registrierung zum Speichern Ihrer Containerimages verwenden. Zwei beliebte Docker-Registrierungsdienste sind [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) und [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). In diesem Tutorial wird Azure Container Registry verwendet. 

Sollten Sie noch keine Containerregistrierung besitzen, führen Sie die folgenden Schritte aus, um eine neue Containerregistrierung in Azure zu erstellen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Optionen **Ressource erstellen** > **Container** > **Container Registry** aus.

2. Geben Sie die folgenden Werte an, um Ihre Containerregistrierung zu erstellen:

   | Feld | Wert | 
   | ----- | ----- |
   | Registrierungsname | Geben Sie einen eindeutigen Namen an. |
   | Abonnement | Wählen Sie ein Abonnement aus der Dropdownliste aus. |
   | Ressourcengruppe | Verwenden Sie zur Vereinfachung der Verwaltung dieselbe Ressourcengruppe für alle Testressourcen, die Sie während der IoT Edge-Schnellstarts und -Tutorials erstellen. Beispielsweise **IoTEdgeResources**. |
   | Standort | Wählen Sie einen Standort in Ihrer Nähe aus. |
   | Administratorbenutzer | Legen Sie **Aktivieren** fest. |
   | SKU | Wählen Sie **Basic** aus. | 

5. Klicken Sie auf **Erstellen**.

6. Navigieren Sie nach der Erstellung der Containerregistrierung zu dieser Registrierung, und klicken Sie anschließend auf **Zugriffsschlüssel**. 

7. Kopieren Sie die Werte für **Anmeldeserver**, **Benutzername** und **Kennwort**. Sie verwenden diese Werte an späterer Stelle des Tutorials, um Zugriff auf die Containerregistrierung zu gewähren. 

## <a name="create-an-iot-edge-module-project"></a>Erstellen eines IoT Edge-Modulprojekts
Mit den folgenden Schritten wird ein IoT Edge-Modulprojekt erstellt, das auf dem Azure IoT Edge-Maven-Vorlagenpaket und dem Azure IoT Java-Geräte-SDK basiert. Sie erstellen das Projekt, indem Sie Visual Studio Code und die Azure IoT-Tools verwenden.

### <a name="create-a-new-solution"></a>Erstellen einer neuen Lösung

Erstellen Sie eine Java-Lösungsvorlage, die Sie mit eigenem Code anpassen können. 

1. Klicken Sie in Visual Studio Code auf **Ansicht** > **Befehlspalette**, um die VS Code-Befehlspalette zu öffnen. 

2. Geben Sie in der Befehlspalette den Befehl **Azure IoT Edge: New IoT Edge Solution** ein, und führen Sie ihn aus. Folgen Sie den Anweisungen in der Befehlspalette, um Ihre Projektmappe zu erstellen.

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein, oder übernehmen Sie den Standardnamen **EdgeSolution**. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Java-Modul** aus. |
   | Provide value for groupId (Wert für groupId eingeben) | Geben Sie einen Wert für die Gruppen-ID ein, oder übernehmen Sie den Standardwert **com.edgemodule**. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **JavaModule**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für das Containerimage wird der von Ihnen im vorherigen Schritt angegebene Name übernommen. Ersetzen Sie **localhost:5000** durch den Anmeldeserverwert aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br>Das endgültige Imagerepository sieht wie folgt aus: \<Registrierungsname\>.azurecr.io/javamodule. |
 
   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-java-module/repository.png)
   
Wenn Sie zum ersten Mal ein Java-Modul erstellen, dauert das Herunterladen der Maven-Pakete unter Umständen mehrere Minuten. Das VS Code-Fenster lädt den Arbeitsbereich für Ihre IoT Edge-Projektmappe. Der Arbeitsbereich der Projektmappe enthält fünf Komponenten auf oberster Ebene. Der Ordner **Module** enthält den Java-Code für Ihr Modul sowie Docker-Dateien für die Erstellung Ihres Moduls als Containerimage. In der Datei **\.env** werden Ihre Anmeldeinformationen für die Containerregistrierung gespeichert. Die Datei **deployment.template.json** enthält die Informationen, die die IoT Edge-Runtime zum Bereitstellen von Modulen auf einem Gerät verwendet. Die Datei **deployment.debug.template.json** enthält die Debugversion der Module. In diesem Tutorial wird nicht der Ordner **\.vscode** oder die Datei **\.gitignore** bearbeitet. 

Wenn Sie beim Erstellen Ihrer Lösung keine Containerregistrierung angegeben, aber den Standardwert „localhost:5000“ übernommen haben, besitzen Sie keine Datei vom Typ „\.env“. 

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihre Containerregistrierung und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen. 

1. Öffnen Sie im VS Code-Explorer die ENV-Datei. 
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort**, die Sie aus der Azure-Containerregistrierung kopiert haben. 
3. Speichern Sie diese Datei. 

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

1. Öffnen Sie im VS Code-Explorer **Module** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

5. Fügen Sie am Anfang der Datei den folgenden Code ein, um neue referenzierte Klassen zu importieren.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

5. Fügen Sie die folgende Definition in der **App**-Klasse hinzu. Mit dieser Variablen wird ein Temperaturschwellenwert festgelegt. Die gemessene Computertemperatur wird erst an IoT Hub gemeldet, wenn sie diesen Wert übersteigt. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. Ersetzen Sie die execute-Methode von **MessageCallbackMqtt** durch den folgenden Code. Diese Methode wird aufgerufen, wenn das Modul eine MQTT-Nachricht vom IoT Edge-Hub empfängt. Sie filtert Nachrichten heraus, die Temperaturen unter dem über den Modulzwilling festgelegten Temperaturschwellenwert melden.

    ```java
        private int counter = 0;
       @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    ```

8. Fügen Sie die folgenden zwei statischen internen Klassen zur **App**-Klasse hinzu. Mit diesen Klassen wird die Variable „tempThreshold“ aktualisiert, wenn sich die gewünschte Eigenschaft des Modulzwillings ändert. Alle Module haben ihren eigenen Modulzwilling, über den Sie den in einem Modul ausgeführten Code direkt in der Cloud konfigurieren können.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

9. Fügen Sie in der **main**-Methode nach **client.open()** die folgenden Zeilen ein, um die Modulzwillingsupdates zu abonnieren:

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

11. Speichern Sie die Datei „App.java“.

12. Öffnen Sie im VS Code-Explorer die Datei **deployment.template.json** im Arbeitsbereich für Ihre IoT Edge-Projektmappe. Über diese Datei erhält der IoT Edge-Agent die Informationen dazu, welche Module bereitgestellt werden sollen (hier: **tempSensor** und **JavaModule**). Außerdem wird der IoT Edge-Hub angewiesen, wie die Nachrichten weitergeleitet werden sollen. Die Visual Studio Code-Erweiterung fügt zwar automatisch die meisten Informationen ein, die Sie für die Bereitstellungsvorlage benötigen, aber Sie sollten überprüfen, ob alle Angaben für Ihre Lösung stimmen: 

   1. Die Standardplattform Ihrer IoT Edge-Instanz ist auf Ihrer VS Code-Statusleiste auf **amd64** festgelegt. Das bedeutet, dass **JavaModule** auf die Linux-amd64-Version des Images festgelegt ist. Ändern Sie die Standardplattform auf der Statusleiste von **amd64** in **arm32v7** oder **windows-amd64**, sofern es sich dabei um die Architektur Ihres IoT Edge-Geräts handelt. 

      ![Aktualisieren der Modulimageplattform](./media/tutorial-java-module/image-platform.png)

   2. Stellen Sie sicher, dass die Vorlage den richtigen Modulnamen und nicht den Standardnamen **SampleModule** besitzt, den Sie beim Erstellen der IoT Edge-Lösung geändert haben.

   3. Im Abschnitt **registryCredentials** werden Ihre Anmeldeinformationen für die Docker-Registrierung gespeichert, damit der IoT Edge-Agent Ihr Modulimage per Pullvorgang abrufen kann. Die tatsächlichen Benutzername-/Kennwortpaare werden in der ENV-Datei gespeichert. Diese Datei wird von Git ignoriert. Fügen Sie Ihre Anmeldeinformationen der ENV-Datei hinzu, falls Sie dies noch nicht durchgeführt haben.  

   4. Weitere Informationen zu Bereitstellungsmanifesten finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md).

13. Fügen Sie den Modulzwilling **JavaModule** zum Bereitstellungsmanifest hinzu. Fügen Sie am Ende des Abschnitts **moduleContent** nach dem Modulzwilling **$edgeHub** den folgenden JSON-Inhalt ein: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Hinzufügen des Modulzwillings zur Bereitstellungsvorlage](./media/tutorial-java-module/module-twin.png)

14. Speichern Sie die Datei „deployment.template.json“.

## <a name="build-your-iot-edge-solution"></a>Erstellen Ihrer IoT Edge-Projektmappe

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und dem **JavaModule**-Element Code hinzugefügt, mit dem Nachrichten herausgefiltert werden, deren gemeldete Computertemperatur unter dem zulässigen Grenzwert liegt. Erstellen Sie nun die Projektmappe als Containerimage, und übertragen Sie sie per Pushvorgang an die Containerregistrierung. 

1. Melden Sie sich bei Docker an. Geben Sie hierzu im Visual Studio Code-Terminal den folgenden Befehl ein. Anschließend können Sie Ihr Modulimage an Ihre Azure-Containerregistrierung pushen.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Verwenden Sie den Benutzernamen, das Kennwort und den Anmeldeserver, die Sie im ersten Abschnitt aus Ihrer Azure-Containerregistrierung kopiert haben. Diese Werte finden Sie alternativ auch im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.

2. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei „deployment.template.json“, und wählen Sie anschließend **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und übertragen) aus. 

Wenn Sie Visual Studio Code anweisen, die Projektmappe zu erstellen, wird zunächst basierend auf den Informationen in der Bereitstellungsvorlage eine Datei vom Typ „deployment.json“ in einem neuen Ordner namens **config** erstellt. Anschließend werden zwei Befehle im integrierten Terminal ausgeführt: `docker build` und `docker push`. Diese beiden Befehle erstellen Ihren Code, packen die Java-App in Container und pushen den Code anschließend an die Containerregistrierung, die Sie beim Initialisieren der Projektmappe angegeben haben. 

Sie können die vollständige Adresse des Containerimages mit Tag im integrierten VS Code-Terminal anzeigen. Die Imageadresse wird auf der Grundlage der Informationen in der Datei „module.json“ erstellt und hat folgendes Format: \<Repository\>:\<Version\>-\<Platform\>. In diesem Tutorial sollte sie wie folgt aussehen: registryname.azurecr.io/javamodule:0.0.1-amd64.

>[!TIP]
>Führen Sie die folgenden Überprüfungen durch, wenn beim Versuch, Ihr Modul zu erstellen und zu pushen, ein Fehler auftritt:
>* Haben Sie sich mit den Anmeldeinformationen aus Ihrer Containerregistrierung bei Docker in Visual Studio Code angemeldet? Dabei handelt es sich um andere Anmeldeinformationen, als Sie zur Anmeldung beim Azure-Portal verwenden.
>* Ist Ihr Containerrepository richtig? Öffnen Sie **modules** > **cmodule** > **module.json**, und suchen Sie nach dem Feld **Repository**. Das Imagerepository sollte wie folgt aussehen: **\<Registrierungsname\>.azurecr.io/javamodule**. 
>* Erstellen Sie die gleiche Art von Containern, die von Ihrem Entwicklungscomputer ausgeführt wird? Visual Studio Code ist standardmäßig auf Container vom Typ „Linux amd64“ festgelegt. Wenn auf Ihrem Entwicklungscomputer Windows-Container oder Container vom Typ „Linux arm32v7“ ausgeführt werden, aktualisieren Sie die Plattform auf der blauen Statusleiste am unteren Rand der VS Code-Fensters so, dass sie Ihrer Containerplattform entspricht.

## <a name="deploy-and-run-the-solution"></a>Bereitstellen und Ausführen der Projektmappe

Im Schnellstartartikel, anhand dessen Sie Ihr IoT Edge-Gerät eingerichtet haben, haben Sie ein Modul über das Azure-Portal bereitgestellt. Sie können Module auch mithilfe der Azure IoT Hub-Toolkit-Erweiterung (bisher als Azure IoT-Toolkit-Erweiterung bekannt) für Visual Studio Code bereitstellen. Sie haben bereits ein Bereitstellungsmanifest für Ihr Szenario vorbereitet: die Datei **deployment.json**. Nun müssen Sie nur noch ein Gerät auswählen, um die Bereitstellung zu empfangen.

1. Geben Sie in der Befehlspalette von VS Code den Befehl **Azure: Sign in** ein, und führen Sie ihn aus, und befolgen Sie die Anweisungen zum Anmelden bei Ihrem Azure-Konto. Falls Sie bereits angemeldet sind, können Sie diesen Schritt überspringen.

2. Führen Sie in der Befehlspalette von VS Code den Befehl **Azure IoT Hub: Select IoT Hub** aus. 

3. Wählen Sie das Abonnement und den IoT-Hub aus, die das zu konfigurierende IoT Edge-Gerät enthalten. 

4. Erweitern Sie im VS Code-Explorer den Abschnitt **Azure IoT Hub-Geräte**. 

5. Klicken Sie mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und klicken Sie dann auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen). 

   ![Erstellen einer Bereitstellung für ein einzelnes Gerät](./media/tutorial-java-module/create-deployment.png)

6. Wählen Sie im Konfigurationsordner (**config**) die Datei **deployment.json** aus, und klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen). Verwenden Sie nicht die Datei „deployment.template.json“. 

7. Klicken Sie auf die Schaltfläche „Aktualisieren“. Nun sollte das neue **JavaModule**-Modul zusammen mit dem **TempSensor**-Modul sowie mit **$edgeAgent** und **$edgeHub** ausgeführt werden.  

## <a name="view-generated-data"></a>Anzeigen generierter Daten

Sobald Sie das Bereitstellungsmanifest auf Ihr IoT Edge-Gerät angewendet haben, erfasst die IoT Edge-Runtime auf dem Gerät die neuen Bereitstellungsinformationen und verwendet sie bei der Ausführung. Alle auf dem Gerät ausgeführten Module, die nicht im Bereitstellungsmanifest enthalten sind, werden beendet. Module, die auf dem Gerät fehlen, werden gestartet. 

Der Status Ihres IoT Edge-Geräts wird im Abschnitt **Azure IoT Hub Devices** (Azure IoT Hub-Geräte) des Visual Studio Code-Explorers angezeigt. Erweitern Sie die Details Ihres Geräts, um eine Liste mit den bereitgestellten und ausgeführten Modulen anzuzeigen. 

Auf dem IoT Edge-Gerät können Sie den Status Ihrer Bereitstellungsmodule mit dem Befehl `iotedge list` anzeigen. Es sollten vier Module angezeigt werden: die beiden IoT Edge-Runtime-Module, „tempSensor“ und das benutzerdefinierte Modul, das Sie in diesem Tutorial erstellt haben. Es kann einige Minuten dauern, bis alle Module gestartet wurden. Führen Sie den Befehl daher erneut aus, falls nicht gleich alle Module angezeigt werden. 

Mit dem Befehl `iotedge logs <module name>` können Sie die Nachrichten anzeigen, die von einem Modul generiert werden. 

Mithilfe von Visual Studio Code können Sie die bei Ihrem IoT-Hub eingehenden Nachrichten anzeigen. 

1. Klicken Sie zum Überwachen von Daten, die bei der IoT Hub-Instanz eingehen, auf die Auslassungspunkte (**...**) und anschließend auf **Start Monitoring D2C Messages** (Überwachung von D2C-Nachrichten starten).
2. Wenn Sie die D2C-Nachrichten für ein bestimmtes Gerät überwachen möchten, klicken Sie mit der rechten Maustaste auf das Gerät in der Liste, und klicken Sie dann auf **Start Monitoring D2C Messages** (Überwachung von D2C-Nachrichten starten).
3. Wenn Sie die Datenüberwachung beenden möchten, führen Sie in der Befehlspalette den Befehl **Azure IoT Hub: Stop monitoring D2C message** aus. 
4. Klicken Sie zum Anzeigen oder Aktualisieren des Modulzwillings mit der rechten Maustaste auf das Modul in der Liste, und klicken Sie auf **Edit module twin** (Modulzwilling bearbeiten). Speichern Sie zum Aktualisieren des Modulzwillings die JSON-Datei des Zwillings, klicken Sie mit der rechten Maustaste auf den Editorbereich, und klicken Sie anschließend auf **Update Module Twin** (Modulzwilling aktualisieren).
5. Installieren Sie zum Anzeigen von Docker-Protokollen [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) für VS Code. Ihre ausgeführten Module finden Sie lokal im Docker-Explorer. Klicken Sie zum Anzeigen im integrierten Terminal im Kontextmenü auf **Protokolle anzeigen**.
 
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen. 

Andernfalls können Sie die in diesem Artikel erstellten lokalen Konfigurationen und Azure-Ressourcen löschen, um Kosten zu vermeiden. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein IoT Edge-Modul mit Code zum Filtern von Rohdaten erstellt, die von Ihrem IoT Edge-Gerät generiert werden. In den nächsten Tutorials erfahren Sie, wie Ihnen Azure IoT Edge noch dabei helfen kann, Daten in geschäftliche Erkenntnisse auf Edge-Ebene zu verwandeln.

> [!div class="nextstepaction"]
> [Speichern von Daten im Edge-Bereich mit SQL Server-Datenbanken](tutorial-store-data-sql-server.md)

