---
title: Informationen zu ExpressRoute Direct – Azure | Microsoft-Dokumentation
description: Diese Seite enthält eine Übersicht über ExpressRoute Direct (Vorschau).
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 3124e98dd035080c2989849232b978c5d4a563ea
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100736"
---
# <a name="about-expressroute-direct-preview"></a>Informationen zu ExpressRoute Direct (Vorschau)

ExpressRoute Direct bietet Ihnen die Möglichkeit, sich direkt mit dem globalen Netzwerk von Microsoft zu verbinden, und zwar an strategisch über die ganze Welt verteilten Peeringstandorten. ExpressRoute Direct bietet duale Konnektivität mit 100 GBit/s, die eine Aktiv/Aktiv-Konnektivität nach Maß unterstützt.

ExpressRoute Direct bietet u.a. folgende Leistungsmerkmale:

* Umfangreiche Datenerfassung in Diensten wie Azure Storage und Cosmos DB
* Physische Isolierung für Branchen, die Regulierung sowie dedizierte und isolierte Konnektivität erfordern, z. B. Bankwesen, Behörden und Einzelhandel
* Präzise Steuerung der Leitungsverteilung basierend auf Unternehmensbereichen

> [!IMPORTANT]
> ExpressRoute Direkt ist derzeit in der Vorschauphase.
>
> Diese öffentliche Vorschauversion wird ohne Servicelevelvereinbarung bereitgestellt und sollte nicht für Produktionsworkloads verwendet werden. Unter Umständen werden bestimmte Features nicht unterstützt, verfügen über eingeschränkte Funktionen und sind nicht an allen Azure-Standorten verfügbar. Weitere Informationen finden Sie unter [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enroll-in-the-preview"></a>Registrieren in der Vorschau

Bevor Sie ExpressRoute Direct nutzen, müssen Sie Ihr Abonnement für die Vorschauversion registrieren. Senden Sie dafür eine E-Mail mit Ihrer Abonnement-ID an <ExpressRouteDirect@microsoft.com>. Geben Sie in der E-Mail die folgenden Aspekte an:

* Szenarien, für die Sie **ExpressRoute Direct** nutzen können
* Bevorzugte Standorte: Unter [Partner und Peeringstandorte](expressroute-locations-providers.md) finden Sie eine vollständige Liste aller Standorte.
* Zeitrahmen für die Implementierung
* Weitere Fragen

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Vergleich von ExpressRoute mit Dienstanbieter und ExpressRoute Direct

| **ExpressRoute mit Dienstanbieter** | **ExpressRoute Direct** | 
| --- | --- |
| Verwendung von Dienstanbietern, um schnelles Onboarding und schnelle Konnektivität mit der vorhandenen Infrastruktur zu ermöglichen | Erfordert eine Infrastruktur mit 100 GBit/s und die vollständige Verwaltung aller Ebenen
| Integration mit Hunderten von Dienstanbietern, einschließlich Ethernet und MPLS | Direkte/Dedizierte Kapazität für regulierte Branchen und Erfassung sehr umfangreicher Datenmengen |
| Leitungs-SKUs zwischen 50 MBit/s und 10 GBit/s | Kunden können eine Kombination der folgenden Verbindungs-SKUs auswählen: 5 Gbit/s, 10 Gbit/s, 40 Gbit/s, 100 Gbit/s – beschränkt auf maximal 200 Gbit/s
| Für Einzelmandanten optimiert | Für Einzelmandanten/Cloud-Dienstanbieter /mehrere Unternehmensbereiche optimiert

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct-Leitungen

Mit Microsoft Azure ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, in die Microsoft Cloud erweitern. Mit ExpressRoute können Sie Verbindungen zu Microsoft-Clouddiensten herstellen, z.B. Microsoft Azure, Office 365 und Dynamics 365.  

Jeder Peeringstandort hat Zugriff auf das globale Netzwerk von Microsoft und kann standardmäßig auf jede Region in einer geopolitischen Zone sowie über eine Premium-Leitung auf alle globalen Regionen zugreifen.  

Die Funktionalität ist in den meisten Szenarien vergleichbar mit der von Leitungen, die für den Betrieb einen ExpressRoute-Dienstanbieter in Anspruch nehmen. Um eine größere Detailliertheit und neue Funktionen von ExpressRoute Direct zu unterstützen, werden von ExpressRoute Direct-Leitungen bestimmte Schlüsselfunktionen geboten.

## <a name="circuit-skus"></a>Leitungs-SKUs

ExpressRoute Direct unterstützt Szenarien zur Erfassung umfangreicher Datenmengen in Azure Storage und anderen Big Data-Diensten. ExpressRoute-Leitungen für ExpressRoute Direct unterstützen nun auch die Leitungs-SKUs **40 GBit/s** und **100 GBit/s**. Die physischen Portpaare sind nur **100 GBit/s** und können mehrere virtuelle Verbindungen mit Bandbreiten von 5 GBit/s, 10 GBit/s, 40 GBit/s, 100 Gbps aufweisen (bis zu 200 GBit/s in beliebiger Kombination). 

## <a name="vlan-tagging"></a>VLAN-Kennzeichnung

ExpressRoute Direct unterstützt die VLAN-Kennzeichnungen QinQ und Dot1Q.

* Die **VLAN-Kennzeichnung QinQ** ermöglicht isolierte Routingdomänen auf Basis von ExpressRoute-Leitungen. Azure weist bei der Leitungserstellung dynamisch ein S-Tag zu, das nicht geändert werden kann. Jedes Peering auf der Leitung (privat und Microsoft) verwendet ein eindeutiges C-Tag als VLAN. Das C-Tag muss an den ExpressRoute Direct-Ports nicht leitungsübergreifend eindeutig sein.

* Die **VLAN-Kennzeichnung Dot1Q** ermöglicht ein einzelnes gekennzeichnetes VLAN basierend auf Paaren von ExpressRoute Direct-Ports. Das in einem Peering verwendete C-Tag muss für alle Leitungen und Peerings im Paar der ExpressRoute Direct-Ports eindeutig sein.

## <a name="workflow"></a>Workflow

[ ![Workflow](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct bietet die gleiche erstklassige SLA mit redundanten Aktiv/Aktiv-Verbindungen in das globale Microsoft-Netzwerk. Die ExpressRoute-Infrastruktur ist redundant. Konnektivität mit dem globalen Microsoft-Netzwerk ist redundant und vielfältig und kann entsprechend den Kundenanforderungen skaliert werden. In der Vorschauphase gibt es keine SLA, und die Lösung sollte nur für nicht produktionsbezogene Workloads in Betracht gezogen werden.

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von ExpressRoute Direct](expressroute-howto-erdirect.md)
