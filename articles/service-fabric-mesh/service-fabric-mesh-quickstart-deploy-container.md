---
title: 'Schnellstart: Bereitstellen von „Hello World“ in Azure Service Fabric Mesh | Microsoft Docs'
description: Dieser Schnellstart zeigt Ihnen, wie Sie eine Service Fabric Mesh-Anwendung in Azure Service Fabric Mesh bereitstellen.
services: service-fabric-mesh
keywords: Vermeiden Sie es, Schlüsselwörter hinzuzufügen oder zu bearbeiten, ohne Ihren SEO-Experten zurate zu ziehen.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 5ca622602c71976917a07005bf349dd98086327c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336982"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Schnellstart: Bereitstellen von Hallo Welt in Service Fabric Mesh

[Service Fabric Mesh](service-fabric-mesh-overview.md) macht es einfach, Microservicesanwendungen in Azure zu erstellen und zu verwalten, ohne virtuelle Computer bereitstellen zu müssen. In diesem Schnellstart erstellen Sie eine „Hello World“-Anwendung in Azure und stellen sie im Internet bereit. Dieser Vorgang wird mit einem einzelnen Befehl durchgeführt. Innerhalb weniger Minuten sehen Sie diese Ansicht in Ihrem Browser:

![„Hello World“-Anwendung im Browser][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Wenn Sie noch kein Azure-Konto haben, [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="set-up-service-fabric-mesh-cli"></a>Einrichten der Service Fabric Mesh-CLI 
Sie können Azure Cloud Shell oder eine lokale Installation der Azure CLI für diesen Schnellstart verwenden. Installieren Sie das CLI-Erweiterungsmodul von Azure Service Fabric Mesh, indem Sie die folgenden [Anweisungen](service-fabric-mesh-howto-setup-cli.md) befolgen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure
Melden Sie sich in Azure an, und legen Sie Ihr Abonnement fest.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Ressourcengruppe erstellen
Erstellen Sie eine Ressourcengruppe, in der die Anwendung bereitgestellt werden soll. Sie können eine vorhandene Ressourcengruppe verwenden und diesen Schritt überspringen. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung
Erstellen Sie Ihre Anwendung in der Ressourcengruppe mit dem Befehl `az mesh deployment create`.  Führen Sie Folgendes aus:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

Der vorherige Befehl stellt eine Linux-Anwendung mit der [linux.json-Vorlage](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json) bereit. Wenn Sie eine Windows-Anwendung bereitstellen möchten, verwenden Sie die [windows.json-Vorlage](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json). Windows-Containerimages sind größer als Linux-Containerimages und benötigen ggf. mehr Zeit für die Bereitstellung.

Mit diesem Befehl wird der unten gezeigte JSON-Codeausschnitt erstellt. Kopieren Sie im Abschnitt ```outputs``` der JSON-Ausgabe die Eigenschaft ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Diese Informationen stammen aus dem Abschnitt ```outputs``` in der ARM-Vorlage. Dieser Abschnitt verweist auf die Gatewayressource, um die öffentliche IP-Adresse abzurufen: 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Öffnen der Anwendung
Nachdem die Anwendung erfolgreich bereitgestellt wurde, kopieren Sie die öffentliche IP-Adresse für den Dienstendpunkt aus der CLI-Ausgabe. Öffnen Sie die IP-Adresse in einem Webbrowser. Eine Webseite mit dem Azure Service Fabric Mesh-Logo wird angezeigt.

## <a name="check-the-application-details"></a>Überprüfen der Anwendungsdetails
Sie können den Status der Anwendung mit dem Befehl `az mesh app show` überprüfen. Dieser Befehl stellt hilfreiche Informationen bereit, die Sie nachverfolgen können.

Der Name der Anwendung für diesen Schnellstart lautet `helloWorldApp`. Führen Sie zum Erfassen der Details der Anwendungsausführung den folgenden Befehl aus:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Anzeigen der Anwendungsprotokolle

Untersuchen Sie die Protokolle für die bereitgestellte Anwendung mit dem Befehl `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie bereit sind, die Anwendung zu löschen, führen Sie den Befehl [az group delete][az-group-delete] aus, um die Ressourcengruppe und die darin enthaltenen Anwendungs- und Netzwerkressourcen zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen und Bereitstellen von Service Fabric Mesh-Anwendungen finden Sie im Tutorial.
> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen einer Webanwendung mit mehreren Diensten](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
