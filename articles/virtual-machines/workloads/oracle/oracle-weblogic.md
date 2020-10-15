---
title: Cosa sono le soluzioni per l'esecuzione di Oracle WebLogic Server in macchine virtuali di Azure
description: Informazioni su come eseguire Oracle WebLogic Server in Macchine virtuali di Microsoft Azure.
author: rezar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/23/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: c19d6d2513d1bf266d10e90dfb1d6fe79c9410d1
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993454"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Quali sono le soluzioni per eseguire Oracle WebLogic Server in Macchine virtuali di Microsoft Azure?

Questa pagina descrive le soluzioni per l'esecuzione di Oracle WebLogic Server (WLS) in macchine virtuali di Azure. Queste soluzioni sono sviluppate congiuntamente da Oracle e Microsoft.

WLS è un server applicazioni Java leader che esegue alcune delle applicazioni Java aziendali più cruciali in tutto il mondo. WLS costituisce la base middleware per Oracle software suite. Oracle e Microsoft si impegnano per consentire ai clienti di WLS la scelta e la flessibilità di eseguire carichi di lavoro in Azure come piattaforma cloud leader.

Le soluzioni WLS di Azure hanno lo scopo di semplificare il più possibile il trasferimento delle applicazioni Java EE in macchine virtuali di Azure tramite l'automazione della maggior parte delle operazioni standard. Le soluzioni eseguono automaticamente il provisioning di risorse di rete virtuale, archiviazione, Java e Linux. Con il minimo sforzo, WebLogic Server è installato. Le soluzioni possono configurare la sicurezza con un gruppo di sicurezza di rete, il bilanciamento del carico con app Azure gateway e l'autenticazione con Azure Active Directory. È anche possibile connettersi automaticamente al database esistente, tra cui Azure PostgreSQL, Azure SQL e Oracle DB nel cloud Oracle o in Azure. La mappa stradale per le soluzioni include la possibilità di abilitare la registrazione distribuita e la memorizzazione nella cache distribuita tramite la coerenza Oracle. Microsoft e Oracle sono partner per abilitare funzionalità simili a WebLogic e al servizio Azure Kubernetes (AKS).

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="È possibile usare la portale di Azure per distribuire WebLogic Server in Azure":::

Sono disponibili quattro offerte per soddisfare diversi scenari, ovvero un singolo nodo senza un server di amministrazione, un singolo nodo con un server di amministrazione, un cluster e un cluster dinamico. Le offerte sono disponibili gratuitamente. Queste offerte sono descritte e collegate di seguito.

_Le offerte si basano sul modello Bring Your Own License_. Si presuppone che l'utente abbia già ottenuto le licenze appropriate con Oracle e che disponga di una licenza corretta per l'esecuzione di offerte in Azure.

Le offerte supportano una gamma di versioni del sistema operativo, Java e WLS tramite immagini di base, ad esempio WebLogic Server 14 e JDK 11 in Oracle Linux 7,6). Queste immagini di base sono disponibili anche in Azure autonomamente. Le immagini di base sono adatte per i clienti che necessitano di distribuzioni di Azure complesse e personalizzate. Il set corrente di immagini di base è disponibile [qui](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1).

_Se si è interessati a lavorare a stretto contatto con gli scenari di migrazione con il team di progettazione che sviluppa queste offerte, selezionare il pulsante [Contatta](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) l'utente_ nella [pagina Panoramica dell'offerta del Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). I responsabili del programma, gli architetti e i tecnici ti contatteranno a breve e inizieranno a collaborare in stretta collaborazione. La possibilità di collaborare in uno scenario di migrazione è gratuita mentre le offerte sono in fase di sviluppo attivo.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server a nodo singolo

Questa offerta esegue il provisioning di una singola macchina virtuale e ne installa WLS. Non crea un dominio o avvia il server di amministrazione. L'offerta a nodo singolo è utile per scenari con configurazione di dominio altamente personalizzata.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server con server di amministrazione

Questa offerta esegue il provisioning di una singola macchina virtuale e ne installa WLS. Viene creato un dominio e viene avviato il server di amministrazione. È possibile gestire il dominio e iniziare subito le distribuzioni dell'applicazione.

## <a name="oracle-weblogic-server-cluster"></a>Cluster di Oracle WebLogic Server

Questa offerta crea un cluster a disponibilità elevata di macchine virtuali WLS. Il server di amministrazione e tutti i server gestiti vengono avviati per impostazione predefinita. È possibile gestire il cluster e iniziare subito a usare le applicazioni a disponibilità elevata.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster dinamico di Oracle WebLogic Server

Questa offerta crea un cluster dinamico scalabile e a disponibilità elevata di macchine virtuali WLS. Il server di amministrazione e tutti i server gestiti vengono avviati per impostazione predefinita.

Le soluzioni consentiranno un'ampia gamma di architetture di distribuzione pronte per la produzione con la relativa facilità. È possibile soddisfare la maggior parte dei casi di migrazione nel modo più produttivo possibile consentendo di concentrarsi sullo sviluppo di applicazioni aziendali.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="È possibile usare la portale di Azure per distribuire WebLogic Server in Azure":::

Oltre a ciò di cui viene effettuato automaticamente il provisioning dalle soluzioni, i clienti hanno la massima flessibilità per personalizzare ulteriormente le distribuzioni. È probabile che, in base alla distribuzione di applicazioni, i clienti integrino altre risorse di Azure con le relative distribuzioni. I clienti sono invitati a fornire commenti e suggerimenti su come migliorare ulteriormente le soluzioni.

## <a name="next-steps"></a>Passaggi successivi

Esplora le offerte in Azure.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server a nodo singolo](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server con server di amministrazione](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Cluster di Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster dinamico di Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
