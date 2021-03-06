---
title: Codieren der benutzerdefinierten Transformation mit Media Services, Version 3 – Azure | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie mit Azure Media Services v3 eine benutzerdefinierte Transformation codiert wird.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: c62d9132cdd7eb2ebcbecc3c417ad30d368a278a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138703"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Codieren einer benutzerdefinierten Transformation

Bei der Codierung mit Azure Media Services können Sie mit einer der empfohlenen integrierten Voreinstellungen basierend auf in der Branche bewährten Vorgehensweisen schnell einsteigen, wie im Tutorial [Hochladen, Codieren und Streamen von Videos mit APIs](stream-files-tutorial-with-api.md) gezeigt, oder Sie können eine benutzerdefinierte Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen entwickeln. 

> [!Note]
> In Azure Media Services v3 werden alle Codierungsbitraten in Bits pro Sekunde angegeben. Dies ist anders als bei den Voreinstellungen von REST v2 Media Encoder Standard. Beispielsweise würde die Bitrate in V2 mit 128, in V3 jedoch mit 128.000 angegeben.

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Klonen Sie ein GitHub-Repository auf Ihren Computer, das das vollständige .NET Core-Beispiel enthält, indem Sie den folgenden Befehl verwenden:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Das benutzerdefinierte voreingestellte Beispiel befindet sich im Ordner [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Erstellen einer Transformation mit einer benutzerdefinierten Voreinstellung 

Beim Erstellen einer neuen [Transformation](https://docs.microsoft.com/rest/api/media/transforms) müssen Sie angeben, was als Ausgabe generiert werden soll. Der erforderliche Parameter ist ein **TransformOutput**-Objekt, wie im folgenden Code gezeigt. Jedes **TransformOutput**-Objekt enthält eine **Voreinstellung**. Die **Voreinstellung** beschreibt die schrittweisen Anweisungen von Video- und/oder Audioverarbeitungsvorgängen, die verwendet werden sollen, um das gewünschte **TransformOutput**-Objekt zu generieren. Das folgende **TransformOutput**-Objekt erstellt benutzerdefinierte Codec- und Layerausgabeeinstellungen.

Beim Erstellen einer [Transformation](https://docs.microsoft.com/rest/api/media/transforms) sollten Sie zunächst mit der **Get**-Methode überprüfen, ob eine solche bereits vorhanden ist. Der folgende Code zeigt dies.  In Media Services v3 geben **Get**-Methoden für Entitäten **NULL** zurück, wenn die Entität nicht vorhanden ist (eine Überprüfung des Namens ohne Unterscheidung zwischen Groß-/Kleinschreibung).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Nächste Schritte

[Streamingdateien](stream-files-tutorial-with-api.md) 
