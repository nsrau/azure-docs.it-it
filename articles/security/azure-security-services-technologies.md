---
title: Servizi e tecnologie per la sicurezza di Azure | Documentazione Microsoft
description: L'articolo offre un elenco dettagliato dei servizi e delle tecnologie per la sicurezza di Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 13183282e5e607f0052194a474203f97e0160adb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610904"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Servizi e tecnologie per la sicurezza disponibili in Azure

I clienti attuali e futuri di Azure spesso chiedono se è disponibile un elenco di tutte le tecnologie e i servizi relativi alla sicurezza offerti da Azure.

Queste informazioni sono utili ai fini della valutazione delle opzioni per la scelta del provider di servizi cloud. È stato quindi fornito questo elenco per agevolare il processo.

L'elenco verrà modificato e accresciuto nel corso del tempo, parallelamente ad Azure. Ricordare di controllare regolarmente questa pagina per essere sempre aggiornati sulle tecnologie e i servizi relativi alla sicurezza.

## <a name="general-azure-security"></a>Sicurezza di Azure generale
|Service|DESCRIZIONE|
|--------|--------|
|[Centro&nbsp;sicurezza&nbsp;di Azure](../security-center/security-center-intro.md)| Soluzione di protezione dei carichi di lavoro nel cloud che supporta la gestione della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi.|
|[Insieme di credenziali chiave Azure](../key-vault/key-vault-overview.md)| Archivio di segreti sicuro per password, stringhe di connessione e altre informazioni necessarie per il funzionamento delle app. |
|[Log di Monitoraggio di Azure](../log-analytics/log-analytics-overview.md)|Servizio di monitoraggio che raccoglie dati di telemetria e altri dati e fornisce un linguaggio di query e un motore di analisi per offrire informazioni dettagliate operative per le app e le risorse. Può essere usato da solo o con altri servizi, come il Centro sicurezza. |
|[Lab di sviluppo/test Azure](../devtest-lab/devtest-lab-overview.md)|Servizio che consente agli sviluppatori e ai tester di creare rapidamente ambienti in Azure riducendo al minimo gli sprechi e i costi di controllo.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Sicurezza di archiviazione
|Service|DESCRIZIONE|
|------|--------|
| [Crittografia&nbsp;del servizio&nbsp;archiviazione&nbsp;di Azure](../storage/common/storage-service-encryption.md)|Funzionalità di sicurezza che crittografa automaticamente i dati nell'archivio di Azure.   |
|[Archiviazione ibrida crittografata con StorSimple](../storsimple/storsimple-ova-overview.md)| Soluzione di archiviazione integrata che gestisce le attività di archiviazione tra i dispositivi locali e l'archivio cloud di Azure.|
|[Crittografia lato client di Azure](../storage/common/storage-client-side-encryption.md)| Soluzione di crittografia lato client che esegue la crittografia dei dati all'interno delle applicazioni client prima di caricarli in Archiviazione di Azure. Questa soluzione gestisce inoltre la decrittografia dei dati durante il download. |
| [Firme di accesso condiviso di Archiviazione di Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione.  |
|[Chiavi dell'account di archiviazione di Azure](../storage/common/storage-create-storage-account.md)| Metodo di controllo di accesso per l'archiviazione di Azure che viene usato per l'autenticazione quando si accede all'account di archiviazione. |
|[Condivisioni file di Azure con crittografia SMB 3.0](../storage/files/storage-files-introduction.md)|Tecnologia di sicurezza di rete che abilita la crittografia di rete automatica per il protocollo di condivisione file Server Message Block (SMB). |
|[Analisi archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/Storage-Analytics)| Tecnologia per la registrazione e la generazione di metriche per i dati nell'account di archiviazione. |

<!------>

## <a name="database-security"></a>Sicurezza del database
|Service|DESCRIZIONE|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](../sql-database/sql-database-firewall-configure.md)|Funzionalità di controllo di accesso di rete che protegge dagli attacchi basati sulla rete al database. |
|[Crittografia&nbsp;a livello&nbsp;di cella&nbsp;SQL di Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Tecnologia di protezione del database che supporta la crittografia a livello granulare.  |
| [Crittografia della connessione&nbsp;SQL&nbsp;di Azure](../sql-database/sql-database-control-access.md)|Per garantire la sicurezza, il database SQL controlla l'accesso con regole del firewall che limitano la connettività in base all'indirizzo IP, meccanismi di autenticazione che richiedono agli utenti di dimostrare la propria identità e meccanismi di autorizzazione che consentono agli utenti di usufruire solo di azioni e dati specifici. |
| [Crittografia sempre attiva SQL di Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Protegge i dati sensibili, ad esempio i numeri di carta di credito o i numeri di identificazione nazionale, come i codici fiscali, archiviati nei database SQL di Azure o nei database di SQL Server.  |
| [Transparent Data Encryption&nbsp;SQL di&nbsp;Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funzionalità di sicurezza del database che crittografa l'archivio di un intero database. |
| [Controllo del database SQL di Azure](../sql-database/sql-database-auditing.md)|Funzionalità di controllo del database SQL che tiene traccia degli eventi che si verificano nel database e li scrive in un log di controllo nell'account di archiviazione di Azure.  |


## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso
|Service|DESCRIZIONE|
|------|--------|
| [Controllo degli accessi&nbsp;in base&nbsp;al ruolo&nbsp;di Azure](../active-directory/role-based-access-control-configure.md)|Funzionalità di controllo di accesso progettata per consentire agli utenti di accedere solo alle risorse necessarie in base ai rispettivi ruoli all'interno dell'organizzazione.  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|Repository di autenticazione basato sul cloud che supporta una directory multi-tenant basata sul cloud e più servizi di gestione delle identità all'interno di Azure.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Servizio di gestione delle identità che consente di controllare il modo in cui i clienti si iscrivono, accedono e gestiscono i rispettivi profili durante l'uso delle applicazioni basate su Azure.   |
| [Servizi di dominio Azure Active Directory](../active-directory-domain-services/active-directory-ds-overview.md)| Versione basata su cloud e gestita di Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| Infrastruttura di sicurezza che si avvale di diverse forme di autenticazione e verifica prima di consentire l'accesso a informazioni protette. |

## <a name="backup-and-disaster-recovery"></a>Backup e ripristino di emergenza
|Service|DESCRIZIONE|
|------|--------|
| [Backup&nbsp;di Azure](../backup/backup-introduction-to-azure-backup.md)| Servizio basato su Azure usato per eseguire il backup e il ripristino dei dati nel cloud di Azure. |
| [Azure&nbsp;Site&nbsp;Recovery](../site-recovery/site-recovery-overview.md)|Servizio online che replica i carichi di lavoro in esecuzione su computer fisici e macchine virtuali da un sito primario in una località secondaria per rendere possibile il ripristino dei servizi dopo un guasto. |

## <a name="networking"></a>Rete
|Service|DESCRIZIONE|
|------|--------|
| [Gruppi&nbsp;di sicurezza&nbsp;di rete](../virtual-network/virtual-networks-nsg.md)| Funzionalità di controllo di accesso basata sulla rete che usa una tupla a 5 elementi per prendere decisioni in merito alla concessione o alla negazione dell'accesso.  |
| [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Dispositivo di rete usato come endpoint VPN per consentire l'accesso cross-premise alle reti virtuali di Azure.  |
| [Gateway applicazione Azure](../application-gateway/application-gateway-introduction.md)|Servizio di bilanciamento del carico avanzato per applicazioni Web, in grado di eseguire il routing in base a URL e che supporta l'offload SSL. |
|[Web application firewall](../application-gateway/waf-overview.md) (WAF)|Funzionalità del gateway applicazione che offre protezione centralizzata da exploit e vulnerabilità comuni per le applicazioni Web|
| [Servizio di bilanciamento del carico di Azure](../load-balancer/load-balancer-overview.md)|Servizio di bilanciamento del carico di rete per applicazioni TCP/UDP. |
| [ExpressRoute di Azure](../expressroute/expressroute-introduction.md)| Collegamento WAN dedicato tra reti locali e reti virtuali di Azure. |
| [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md)| Servizio di bilanciamento del carico DNS globale.|
| [Proxy di applicazione Azure](../active-directory/active-directory-application-proxy-get-started.md)| Front-end di autenticazione usato per proteggere l'accesso remoto per le applicazioni Web ospitate in locale. |
|[Firewall di Azure](../firewall/overview.md)|Servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure.|
|[Protezione DDoS di Azure](../virtual-network/ddos-protection-overview.md)|Insieme alle procedure consigliate di progettazione delle applicazioni, offre un meccanismo di difesa dagli attacchi DDoS (Distributed Denial of Service).|
|[Endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md)|Estende lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale ai servizi di Azure tramite una connessione diretta.|