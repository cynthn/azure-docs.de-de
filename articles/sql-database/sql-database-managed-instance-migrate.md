---
title: Migrieren einer SQL Server-Instanz zu einer verwalteten Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine SQL Server-Instanz zu einer verwalteten Azure SQL-Datenbank-Instanz migrieren.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098356"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migration einer SQL Server-Instanz zu einer verwalteten Azure SQL-Datenbank-Instanz

Dieser Artikel enthält Informationen zu den Methoden, mit denen eine SQL Server 2005-Instanz (oder eine höhere Version) zu einer [verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance.md) migriert wird. Informationen zum Migrieren zu einem Singleton oder einem Pool für elastische Datenbanken finden Sie unter [Migrieren zu einem Singleton oder einer in einem Pool zusammengefassten Datenbank](sql-database-cloud-migrate.md). Weitere Informationen zum Migrieren von anderen Plattformen finden Sie im [Leitfaden zur Azure-Datenbankmigration](https://datamigration.microsoft.com/).

Die Datenbankmigration sieht im Allgemeinen folgendermaßen aus:

![Migrationsprozess](./media/sql-database-managed-instance-migration/migration-process.png)

- [Bewerten der Kompatibilität der verwalteten Instanz](#assess-managed-instance-compatibility)
- [Wählen einer App-Konnektivitätsoption](sql-database-managed-instance-connect-app.md)
- [Bereitstellen für eine verwaltete Instanz mit optimaler Größe](#deploy-to-an-optimally-sized-managed-instance)
- [Auswählen der Migrationsmethode und Migration](#select-migration-method-and-migrate)
- [Überwachen von Anwendungen](#monitor-applications)

> [!NOTE]
> Informationen zum Migrieren einer einzelnen Datenbank zu einer Einzeldatenbank oder einem Pool für elastische Datenbanken finden Sie unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank in der Cloud](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Bewerten der Kompatibilität der verwalteten Instanz

Stellen Sie zunächst fest, ob die verwaltete Instanz mit den Datenbankanforderungen Ihrer Anwendung kompatibel ist. Die Bereitstellungsoption „Verwaltete Instanz“ wurde konzipiert, um eine einfache Migration per Lift & Shift für die meisten bestehenden Anwendungen zu ermöglichen, die SQL Server lokal oder auf virtuellen Computern verwenden. Es kann jedoch vorkommen, dass Sie Features oder Funktionen benötigen, die noch nicht unterstützt werden und die Kosten für die Implementierung eines Workarounds zu hoch sind.

Verwenden Sie den [Datenmigrations-Assistenten (DMA)](https://docs.microsoft.com/sql/dma/dma-overview), um mögliche Kompatibilitätsprobleme zu erkennen, die die Datenbankfunktionalität der Azure SQL-Datenbank beeinträchtigen. Der DMA unterstützt noch keine verwalteten Instanzen als Migrationsziel, aber es wird empfohlen, die Bewertung der Azure SQL-Datenbank durchzuführen und die Liste der gemeldeten Funktionsparitäten und Kompatibilitätsprobleme anhand der Produktdokumentation sorgfältig zu überprüfen. Unter [Funktionen von Azure SQL-Datenbank](sql-database-features.md) können Sie überprüfen, ob gemeldete Blockierungsprobleme vorliegen, die keine Blockierungen in einer verwalteten Instanz sind, da die meisten Blockierungsprobleme, die eine Migration zu Azure SQL-Datenbank verhindern, mit der verwalteten Instanz beseitigt wurden. Beispielsweise stehen Features wie datenbankübergreifende Abfragen, datenbankübergreifende Transaktionen innerhalb derselben Instanz, verknüpfte Server mit anderen SQL-Quellen, CLR, globale temporäre Tabellen, Ansichten auf Instanzebene, Service Broker und ähnliches in verwalteten Instanzen zur Verfügung.

Wenn gemeldete Blockierungsprobleme vorliegen, die in der Bereitstellungsoption „Verwaltete Instanz“ nicht behoben wurden, müssen Sie möglicherweise eine andere Möglichkeit in Betracht ziehen, z.B. [SQL Server auf virtuellen Azure-Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/). Hier einige Beispiele:

- Wenn Sie direkten Zugriff auf das Betriebssystem oder das Dateisystem benötigen, z.B. um Drittanbieteragents oder benutzerdefinierte Agents auf demselben virtuellen SQL Server-Computer zu installieren.
- Wenn Sie unbedingt von Features abhängig sind, die noch nicht unterstützt werden, wie z.B. FileStream / FileTable, PolyBase und instanzübergreifende Transaktionen.
- Wenn Sie unbedingt bei einer bestimmten Version von SQL Server (z.B. 2012) bleiben müssen.
- Wenn Ihre Computeanforderungen wesentlich niedriger sind als bei einer verwalteten Instanz (z.B. ein virtueller Kern) und eine Datenbankkonsolidierung nicht in Frage kommt.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Bereitstellen für eine verwaltete Instanz mit optimaler Größe

Verwaltete Instanzen sind auf lokale Workloads zugeschnitten, die zum Verschieben in die Cloud vorgesehen sind. Sie führen ein [neues Kaufmodell](sql-database-service-tiers-vcore.md) ein, das eine größere Flexibilität bei der Auswahl der richtigen Ressourcen für Ihre Workloads bietet. In der lokalen Umgebung sind Sie wahrscheinlich daran gewöhnt, diese Workloads mit physischen Kernen und E/A-Bandbreite zu dimensionieren. Das Erwerbsmodell für verwaltete Instanzen basiert auf virtuellen Kernen oder „V-Kernen“, wobei zusätzlicher Speicher und EA separat erhältlich sind. Das V-Kern-Modell ist eine einfachere Methode, um die Computeanforderungen in der Cloud mit dem, was Sie heute lokal verwenden, zu vergleichen. Mit diesem neuen Modell können Sie die Größe Ihrer Zielumgebung in der Cloud anpassen.

Im [Azure-Portal](sql-database-scale-resources.md) haben Sie die Möglichkeit, Compute- und Speicherressourcen zum Zeitpunkt der Bereitstellung auszuwählen und anschließend zu ändern, ohne dass es zu Downtime für Ihre Anwendung kommt:

![Dimensionierung einer verwalteten Instanz](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Informationen zum Erstellen der VNET-Infrastruktur und einer verwalteten Instanz finden Sie unter [Erstellen einer verwalteten Instanz](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Es ist wichtig, dass Sie Ihr Ziel-VNET und -subnetz immer in Übereinstimmung mit den [VNET-Anforderungen für verwaltete Instanzen](sql-database-managed-instance-connectivity-architecture.md#network-requirements) halten. Jede Inkompatibilität kann Sie daran hindern, neue Instanzen anzulegen oder bereits erstellte Instanzen zu verwenden. Erfahren Sie mehr über das [Erstellen neuer](sql-database-managed-instance-create-vnet-subnet.md) und [Konfigurieren vorhandener](sql-database-managed-instance-configure-vnet-subnet.md) Netzwerke.

## <a name="select-migration-method-and-migrate"></a>Auswählen der Migrationsmethode und Migration

Die Bereitstellungsoption „Verwaltete Instanz“ zielt auf Benutzerszenarien ab, die eine Massenmigration der Datenbank von lokalen oder IaaS-Datenbankimplementierungen erfordern. Sie sind die optimale Wahl, wenn es darum geht, das Back-End der Anwendungen, die regelmäßig Funktionalitäten auf Instanzebene und/oder über Datenbanken hinweg nutzen, per Lift & Shift zu migrieren. Wenn dies Ihr Szenario ist, können Sie eine ganze Instanz in eine entsprechende Umgebung in Azure verschieben, ohne dass Sie Ihre Anwendungen neu strukturieren müssen.

Zum Verschieben von SQL-Instanzen müssen Sie Folgendes sorgfältig planen:

- Die Migration aller Datenbanken, die zusammengeführt werden müssen (diejenigen, die auf derselben Instanz ausgeführt werden).
- Die Migration von Objekten auf Instanzebene, von denen Ihre Anwendung abhängt, wie Anmeldeinformationen, SQL-Agentaufträge und -operatoren sowie Trigger auf Serverebene.

Verwaltete Instanz ist ein verwalteter Dienst, der es Ihnen ermöglicht, einige der regulären DBA-Aktivitäten während deren Einbindung an die Plattform zu delegieren. Daher müssen einige Daten auf Instanzebene nicht migriert werden, wie z.B. Wartungsaufträge für regelmäßige Sicherungen oder Always On-Konfigurationen, da [Hochverfügbarkeit](sql-database-high-availability.md) integriert ist.

Verwaltete Instanzen unterstützen die folgenden Datenbankmigrationsoptionen (derzeit sind dies die einzigen unterstützten Migrationsmethoden):

- Azure Database Migration Service – Migrationen nahezu ohne Ausfallzeiten
- Native `RESTORE DATABASE FROM URL` – nutzt native Sicherungen von SQL Server und ist mit einer gewissen Ausfallzeit verbunden

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

Der [Azure Database Migration Service (DMS)](../dms/dms-overview.md) ist ein vollständig verwalteter Dienst, der die nahtlose Migration von mehreren Datenbankquellen zu Azure-Datenplattformen mit minimaler Downtime ermöglicht. Dieser Dienst optimiert die Aufgaben, die erforderlich sind, um bestehende Drittanbieter- und SQL Server-Datenbanken in Azure zu verschieben. Zu den Bereitstellungsoptionen während der öffentlichen Vorschau gehören Datenbanken in Azure SQL-Datenbank und SQL Server-Datenbanken auf einem virtuellen Azure-Computer. DMS ist die empfohlene Migrationsmethode für Ihre Unternehmensworkloads.

Wenn Sie SQL Server Integration Services (SSIS) auf SQL Server lokal verwenden, unterstützt DMS noch nicht die Migration des SSIS-Katalogs (SSISDB), in dem SSIS-Pakete gespeichert werden. Sie können jedoch die Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) bereitstellen, mit der eine neue SSISDB in einer verwalteten Instanz erstellt wird. Anschließend können Sie dort das Paket erneut bereitstellen. Siehe dazu [Erstellen der Azure-SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Weitere Informationen zu diesem Szenario und zu Konfigurationsschritten für DMS finden Sie unter [Migrieren Ihrer lokalen Datenbank zu einer verwalteten Instanz mit DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Native RESTORE FROM URL-Option

Das Wiederherstellen mit der RESTORE-Option von nativen Sicherungen (BAK-Dateien), die von lokalen SQL Server-Instanzen oder von [SQL Server auf virtuellen Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/) erstellt wurden, die in [Azure Storage](https://azure.microsoft.com/services/storage/) verfügbar sind, ist eine der wichtigsten Funktionen der Bereitstellungsoption „Verwaltete Instanz“, die eine schnelle und einfache Offlinedatenbankmigration ermöglicht.

Das folgende Diagramm bietet einen allgemeinen Überblick über den Prozess:

![Migrationsflow](./media/sql-database-managed-instance-migration/migration-flow.png)

In der folgenden Tabelle finden Sie weitere Informationen über die Methoden, die Sie je nach Version der ausgeführten SQL Server-Quellinstanz verwenden können:

|Schritt|SQL-Engine und -Version|Sicherungs- und Wiederherstellungsmethode|
|---|---|---|
|Sicherung auf Azure Storage legen|Vor SQL 2012 SP1 CU2|Direkter Upload der .bak-Datei in Azure Storage|
||2012 SP1 CU2 – 2016|Direkte Sicherung mit veralteter [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)-Syntax|
||Ab 2016|Direkte Sicherung mit [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Wiederherstellung aus Azure Storage in eine verwaltete Instanz|[RESTORE FROM URL mit SAS CREDENTIAL](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Beim Migrieren einer Datenbank, die durch [Transparent Data Encryption](transparent-data-encryption-azure-sql.md) geschützt ist, zu einer verwalteten Instanz mithilfe der nativen Wiederherstellungsoption muss das entsprechende Zertifikat von der lokalen oder der IaaS-SQL Server-Instanz vor der Wiederherstellung der Datenbank migriert werden. Eine ausführliche Anleitung finden Sie unter [Migrieren von TDE zur verwalteten Instanz](sql-database-managed-instance-migrate-tde-certificate.md).
> - Das Wiederherstellen von Systemdatenbanken wird nicht unterstützt. Um Objekte auf Instanzebene (gespeichert in Master- oder msdb-Datenbanken) zu migrieren, wird empfohlen, diese zu skripten und T-SQL-Skripts auf der Zielinstanz auszuführen.

Einen Schnellstart, der zeigt, wie Sie eine Datenbanksicherung mithilfe von SAS-Anmeldeinformationen in einer verwalteten Instanz wiederherstellen, finden Sie unter [Wiederherstellen einer Datenbanksicherung in einer verwalteten Instanz](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Überwachen von Anwendungen

Verfolgen Sie das Anwendungsverhalten und die Leistung nach der Migration. In einer verwalteten Instanz werden einige Änderungen erst dann übernommen, wenn der [Kompatibilitätsgrad der Datenbank geändert wurde](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Die Datenbankmigration zur Azure SQL-Datenbank behält in den meisten Fällen ihren ursprünglichen Kompatibilitätsgrad bei. Wenn der Kompatibilitätsgrad einer Benutzerdatenbank vor der Migration 100 oder höher war, bleibt er auch nach der Migration unverändert. Lag der Kompatibilitätsgrad einer Benutzerdatenbank vor der Migration bei 90, wird er in der aktualisierten Datenbank auf 100 festgelegt, den niedrigsten unterstützten Kompatibilitätsgrad in einer verwalteten Instanz. Der Kompatibilitätsgrad von Systemdatenbanken ist 140.

Um Migrationsrisiken zu minimieren, sollten Sie den Kompatibilitätsgrad der Datenbank erst nach der Leistungsüberwachung ändern. Verwenden Sie den Abfragespeicher als optimales Tool, um Informationen über die Workloadleistung vor und nach einer Änderung des Datenbankkompatibilitätsgrads zu erhalten. Siehe [Aufrechterhalten einer stabilen Leistung während des Upgrades auf das neuere SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Sobald Sie sich auf einer vollständig verwalteten Plattform befinden, profitieren Sie von den Vorteilen, die automatisch als Teil des SQL-Datenbankdienstes bereitgestellt werden. Beispielsweise müssen Sie keine Sicherungen für den Managed Instance-Dienst erstellen, da dieser automatische Sicherungen für Sie durchführt. Sie müssen sich auch nicht mehr darum kümmern, Sicherungen zu planen, zu erstellen und zu verwalten. Verwaltete Instanzen bieten Ihnen die Möglichkeit, mithilfe der [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore) eine Wiederherstellung auf jeden beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums durchzuführen. Darüber hinaus müssen Sie sich keine Gedanken über die Einrichtung von [Hochverfügbarkeit](sql-database-high-availability.md) machen, da diese integriert ist.

Um die Sicherheit zu erhöhen, sollten Sie einige der verfügbaren Features nutzen:

- Azure Active Directory-Authentifizierung auf Datenbankebene
- Verwenden Sie [erweiterte Sicherheitsfunktionen](sql-database-security-overview.md) wie [Überwachung](sql-database-managed-instance-auditing.md), [Bedrohungserkennung](sql-database-advanced-data-security.md), [Sicherheit auf Zeilenebene](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) und die [dynamische Datenmaskierung](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) zum Schutz Ihrer Instanz.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu verwalteten Instanzen finden Sie unter [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md).
- Ein Tutorial zu einer Wiederherstellung aus einer Sicherung finden Sie unter [Erstellen einer verwalteten Instanz](sql-database-managed-instance-get-started.md).
- Ein Tutorial zur Migration mit DMS finden Sie unter [Migrieren Ihrer lokalen Datenbank zu einer verwalteten Instanz mit DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
