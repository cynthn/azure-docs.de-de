---
title: Includedatei
description: Includedatei
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/19/2018
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 9e16c3bb4e4e5ba384bdc664e2c056acb0b70caf
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56448227"
---
### <a name="what-is-expressroute-direct"></a>Was ist ExpressRoute Direct?

ExpressRoute Direct bietet Kunden die Möglichkeit, sich direkt mit dem globalen Netzwerk von Microsoft zu verbinden, und zwar an strategisch über die ganze Welt verteilten Peeringstandorten. ExpressRoute Direct bietet duale Konnektivität mit 100 GBit/s, die eine Aktiv/Aktiv-Konnektivität nach Maß unterstützt. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Wie stellen Kunden eine Verbindung mit ExpressRoute Direct her? 

Kunden müssen mit ihren lokalen Carriern und Co-Location-Anbietern zusammenarbeiten, um eine Verbindung mit ExpressRoute-Routern herzustellen, um die Vorteile von ExpressRoute Direct nutzen zu können.

### <a name="what-locations-will-the-100-gbps-expressroute-direct-be-available-for-public-preview"></a>An welchen Standorten wird die 100-Gbit/s-Konnektivität von ExpressRoute Direct für die öffentliche Vorschau verfügbar sein? 

Eine ausgewählte Anzahl von ExpressRoute-Peeringstandorten unterstützt dies während der öffentlichen Vorschau. Die verfügbaren Ports sind dynamisch und werden in PowerShell verfügbar sein, um die Kapazität anzuzeigen. Die folgenden Standorte sind geplant. Sie können sich *je nach Verfügbarkeit ändern*:

* Amsterdam
* Canberra
* Chicago
* Washington DC
* Dallas 
* Hongkong
* Los Angeles
* New York City
* Paris
* San Antonio
* Silicon Valley
* Singapur 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Welche SLA gilt für ExpressRoute Direct?

ExpressRoute Direct verwendet die gleiche [Unternehmensstufe wie ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Welche Szenarien sollten Kunden mit ExpressRoute Direct in Betracht ziehen?  

ExpressRoute Direct bietet Kunden direkte 100-Gbit/s-Portpaare in den globalen Microsoft-Backbone. Zu den Szenarien, die den Kunden den größten Nutzen bringen, gehören:  massive Datenerfassung, physische Isolierung für regulierte Märkte und dedizierte Kapazität für Burstszenarien wie Rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Welches Abrechnungsmodell gilt für ExpressRoute Direct? 

ExpressRoute Direct wird für das Portpaar zu einem festen Betrag in Rechnung gestellt. Standardverbindungen sind ohne zusätzliche Stunden enthalten, und Premium wird mit einem leichten Aufpreis berechnet. Ausgehende Datenübertragungen werden pro Verbindung basierend auf der Zone des Peeringstandorts abgerechnet.

### <a name="when-does-billing-state-for-the-expressroute-direct-port-pairs"></a>Wann erfolgt die Abrechnung für die ExpressRoute Direct-Portpaare?

ExpressRoute Direct-Portpaare werden 45 Tage nach Beginn der Erstellung der ExpressRoute Direct-Ressource berechnet, oder wenn 1 oder beide Links aktiviert werden, was auch immer zuerst eintritt. Die 45-Tage-Karenzzeit wird Kunden gewährt, damit diese den Querverbindungsprozess mit dem Housinganbieter abschließen können.
