---
title: Integrità risorse per i servizi cloud (versione classica)
description: Questo articolo illustra il supporto di Integrità risorse check (RHC) per Servizi cloud di Microsoft Azure (versione classica)
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 9/1/2020
ms.author: tagore
ms.openlocfilehash: ea25695ddc36571bef3ff61df7de3e71f6f939ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056056"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Supporto di Integrità risorse check (RHC) per servizi cloud di Azure (versione classica)
Questo articolo illustra il supporto di Integrità risorse check (RHC) per [servizi cloud di Microsoft Azure (versione classica)](https://azure.microsoft.com/services/cloud-services)

[Integrità risorse di Azure](https://docs.microsoft.com/azure/service-health/resource-health-overview) per i servizi cloud consente di diagnosticare e ottenere supporto per i problemi del servizio che interessano la distribuzione del servizio cloud, i ruoli & le istanze del ruolo. Segnala l'integrità corrente e passata dei servizi cloud in fase di distribuzione, ruolo & livello di istanza del ruolo.

Azure status segnala i problemi che interessano un'ampia gamma di clienti di Azure. Integrità risorse offre un dashboard personalizzato dell'integrità delle risorse. Integrità risorse Mostra tutte le volte in cui le risorse non sono state disponibili a causa di problemi dei servizi di Azure. Questi dati semplificano la verifica della violazione di un contratto di contratto.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Immagine Visualizza il pannello di controllo integrità risorse nel portale di Azure.":::

## <a name="how-health-is-checked-and-reported"></a>Come viene controllata e segnalata l'integrità?
Integrità risorse viene segnalato a livello di distribuzione o di ruolo. Il controllo dell'integrità si verifica a livello di istanza del ruolo, lo stato viene aggregato e segnalato a livello di ruolo. ad esempio Se tutte le istanze del ruolo sono disponibili, lo stato del ruolo è disponibile. Allo stesso modo, viene aggregato lo stato di integrità di tutti i ruoli e viene segnalato a livello di distribuzione. ad esempio Se tutti i ruoli sono disponibili, lo stato della distribuzione diventa disponibile. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Perché non è possibile visualizzare lo stato di integrità per la distribuzione degli slot di staging?
I controlli di integrità delle risorse funzionano solo per la distribuzione degli slot di produzione. La distribuzione dello slot di gestione temporanea non è ancora supportata. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>Integrità risorse verifica anche l'integrità dell'applicazione?
No, il controllo dell'integrità si verifica solo per le istanze del ruolo e non monitora l'integrità dell'applicazione. ad esempio Anche se 1 di tre istanze del ruolo non sono integre, l'applicazione può essere ancora disponibile. RHC non usa [Probe](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) del servizio di bilanciamento del carico o probe dell'agente guest. I clienti devono quindi continuare a usare i probe del servizio di bilanciamento del carico per monitorare l'integrità dell'applicazione. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Quali sono le annotazioni per i servizi cloud?
Le annotazioni sono lo stato di integrità della distribuzione o dei ruoli. Sono presenti annotazioni diverse in base allo stato di integrità, motivo della modifica dello stato e così via. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>Cosa significa che l'istanza del ruolo non è disponibile?
Ciò significa che l'istanza del ruolo non emette un segnale integro alla piattaforma. Verificare lo stato dell'istanza del ruolo per una spiegazione dettagliata del motivo per cui non viene emesso un segnale integro.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>Cosa significa che la distribuzione è sconosciuta?
Unknown indica che non è possibile determinare l'integrità aggregata della distribuzione del servizio cloud. Ciò indica in genere che non è stata creata alcuna distribuzione di produzione per il servizio cloud, che la distribuzione è stata appena creata (e che Azure sta iniziando a raccogliere gli eventi di integrità) oppure che la piattaforma sta raccogliendo gli eventi di integrità per questa distribuzione.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Perché le annotazioni dell'istanza del ruolo citano le macchine virtuali anziché le istanze del ruolo?
Poiché le istanze del ruolo sono fondamentalmente macchine virtuali e il controllo dell'integrità delle macchine virtuali viene riutilizzato per le istanze del ruolo, per rappresentare le istanze del ruolo viene usato il termine della macchina virtuale. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Annotazioni di servizi cloud (livello di distribuzione) & il significato
| Annotazione | Descrizione | 
| --- | --- | 
| Disponibile| Non sono presenti problemi noti della piattaforma Azure che influiscono sulla distribuzione del servizio cloud |
| Sconosciuto | Non è possibile determinare lo stato di integrità della distribuzione del servizio cloud in questo momento | 
| Configurazione di Integrità risorse | Configurazione dell'integrità delle risorse per questa risorsa. Integrità risorse consente di osservare le risorse di Azure per fornire dettagli sugli eventi in corso e passati che li hanno interessati|
| Degraded | La distribuzione del servizio cloud è degradata. È in corso un tentativo per ripristinare automaticamente la distribuzione del servizio cloud e determinare la causa del problema. Al momento non è richiesta alcuna azione aggiuntiva |
| Unhealthy | La distribuzione del servizio cloud non è integra perché le {0} {1} istanze del ruolo non sono disponibili |
| Degraded | La distribuzione del servizio cloud è danneggiata perché {0} le {1} istanze del ruolo non sono disponibili | 
| Disponibile e probabilmente interessato | La distribuzione del servizio cloud è in esecuzione, tuttavia un'interruzione del servizio di Azure in corso può impedire la connessione. La connettività verrà ripristinata dopo che l'interruzione è stata risolta |
| Non disponibile e probabilmente interessato | Lo stato di questa distribuzione del servizio cloud potrebbe essere influenzato da un'interruzione del servizio di Azure. La distribuzione del servizio cloud verrà ripristinata automaticamente dopo la risoluzione dell'interruzione |
| Sconosciuto e probabilmente interessato | Non è possibile determinare lo stato di integrità della distribuzione del servizio cloud in questo momento. Il problema potrebbe essere causato da un'interruzione del servizio di Azure in corso che potrebbe avere un effetto su questa macchina virtuale, che verrà ripristinata automaticamente quando viene risolta l'interruzione |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Annotazioni dei servizi cloud (a livello di istanza del ruolo) & il significato
| Annotazione | Descrizione | 
| --- | --- | 
| Disponibile | Non sono presenti problemi noti della piattaforma Azure che hanno effetto su questa macchina virtuale | 
| Sconosciuto | Non è possibile determinare lo stato di integrità della macchina virtuale in questo momento |
| Arrestato e deallocazione | La macchina virtuale verrà arrestata e deallocata come richiesto da un utente autorizzato o da un processo |
| Configurazione di Integrità risorse | Configurazione dell'integrità delle risorse per questa risorsa. Integrità risorse consente di osservare le risorse di Azure per fornire dettagli sugli eventi in corso e passati che li hanno interessati |
| Non disponibile | La macchina virtuale non è disponibile. È in corso un tentativo per ripristinare automaticamente la macchina virtuale e determinare la causa del problema. Al momento non è richiesta alcuna azione aggiuntiva |
| Degraded | La macchina virtuale è danneggiata. È in corso un tentativo per ripristinare automaticamente la macchina virtuale e determinare la causa del problema. Al momento non è richiesta alcuna azione aggiuntiva |
| Errore hardware del server host | Questa macchina virtuale è interessata da un errore irreversibile {HardwareCategory} nel server host. Azure ridistribuirà la macchina virtuale in un server host integro |
| Migrazione pianificata a causa di hardware danneggiato | Azure ha rilevato che il server host ha {0} danneggiato che probabilmente genererà un errore a breve. Se possibile, verrà eseguita la migrazione in tempo reale della macchina virtuale il prima possibile, altrimenti verrà ridistribuita dopo le ore {1} UTC. Per ridurre al minimo i rischi per il servizio e, nel caso in cui l'hardware abbia esito negativo prima che venga avviata la migrazione del sistema, è consigliabile eseguire la ridistribuzione automatica della macchina virtuale non appena possibile |
| Disponibile e probabilmente interessato | La macchina virtuale è in esecuzione, tuttavia un'interruzione del servizio di Azure in corso può impedire la connessione. La connettività verrà ripristinata dopo che l'interruzione è stata risolta |
| Non disponibile e probabilmente interessato | Lo stato di integrità di questa macchina virtuale potrebbe essere influenzato da un'interruzione del servizio di Azure. La macchina virtuale verrà ripristinata automaticamente dopo la risoluzione dell'interruzione |
| Sconosciuto e probabilmente interessato | Non è possibile determinare lo stato di integrità della macchina virtuale in questo momento. Il problema potrebbe essere causato da un'interruzione del servizio di Azure in corso che potrebbe avere un effetto su questa macchina virtuale, che verrà ripristinata automaticamente quando viene risolta l'interruzione |
| Risorse hardware allocate | Le risorse hardware sono state assegnate alla macchina virtuale e saranno presto online |
| Arresto e deallocazione | La macchina virtuale verrà arrestata e deallocata come richiesto da un utente autorizzato o da un processo |
| Configurazione in corso di aggiornamento | La configurazione della macchina virtuale viene aggiornata come richiesto da un utente autorizzato o da un processo |
| Riavviato dall'utente | La macchina virtuale verrà riavviata come richiesto da un utente autorizzato o da un processo. Sarà nuovamente online al termine del riavvio |
| Ridistribuzione in un host diverso | La macchina virtuale verrà ridistribuita in un host diverso come richiesto da un utente autorizzato o da un processo. Sarà nuovamente online al termine della ridistribuzione |
| Arrestato dall'utente | La macchina virtuale verrà arrestata come richiesto da un utente autorizzato o da un processo |
| Arrestato dall'utente o dal processo | La macchina virtuale verrà arrestata come richiesto da un utente autorizzato o da un processo in esecuzione all'interno della macchina stessa |
| Avviato dall'utente | La macchina virtuale verrà avviata come richiesto da un utente autorizzato o da un processo. Sarà online a breve |
| Ridistribuzione di manutenzione in un host diverso | Questa macchina virtuale viene ridistribuita in un server host diverso nel quadro di un'attività di manutenzione pianificata. Sarà nuovamente online al termine della ridistribuzione |
| Riavvio avviato dall'interno del computer | È stato attivato un riavvio all'interno della macchina virtuale. Questa operazione potrebbe essere stata causata da un errore del sistema operativo della macchina virtuale o dalla richiesta di un utente o di un processo autorizzato. La macchina virtuale sarà nuovamente online al termine del riavvio |
| Ridimensionato dall'utente | Questa macchina virtuale verrà ridimensionata come richiesto da un utente autorizzato o da un processo. Sarà nuovamente online al termine del ridimensionamento |
| Computer arrestato in modo anomalo | È stato attivato un riavvio all'interno della macchina virtuale. Questa operazione potrebbe essere stata causata da un errore del sistema operativo della macchina virtuale o dalla richiesta di un utente o di un processo autorizzato. La macchina virtuale sarà nuovamente online al termine del riavvio |
| Riavvio manutenzione per aggiornamento host | È in corso l'applicazione di aggiornamenti di manutenzione al server host che esegue questa macchina virtuale. Non è richiesto alcun intervento aggiuntivo da parte dell'utente in questo momento. Sarà nuovamente online al termine della manutenzione |
| Ridistribuzione di manutenzione in un nuovo hardware | La macchina virtuale non è disponibile perché è in corso la ridistribuzione su hardware più recente nel quadro di un evento di manutenzione pianificata. Non è richiesto alcun intervento aggiuntivo da parte dell'utente in questo momento. Sarà nuovamente online al termine della manutenzione pianificata |
| Computer con priorità bassa interrotto | La macchina virtuale è stata annullata. Questa macchina virtuale a priorità bassa viene arrestata e deallocata |
| Riavvio del server host | La macchina virtuale non è disponibile a causa di un riavvio imprevisto del server host. Il server host è in fase di riavvio. Dopo aver completato il riavvio, la macchina virtuale sarà nuovamente online. Al momento non è richiesta alcuna azione aggiuntiva |
| Ridistribuzione a causa di un errore dell'host | La macchina virtuale non è disponibile ed è in corso la ridistribuzione a causa di un errore imprevisto nel server host. Azure ha iniziato il processo di ripristino automatico e al momento sta avviando la macchina virtuale in un host diverso. Al momento non è richiesta alcuna azione aggiuntiva |
| Errore host imprevisto | La macchina virtuale non è disponibile a causa di un errore imprevisto nel server host. Azure ha iniziato il processo di ripristino automatico ed è in corso il riavvio del server host. Non è richiesto alcun intervento aggiuntivo da parte dell'utente in questo momento. La macchina virtuale sarà nuovamente online al termine del riavvio |
| Ridistribuzione a causa di una manutenzione non pianificata dell'host | La macchina virtuale non è disponibile ed è in corso la ridistribuzione a causa di un errore imprevisto nel server host. Azure ha iniziato il processo di ripristino automatico e al momento sta avviando la macchina virtuale in un server host diverso. Al momento non è richiesta alcuna azione aggiuntiva |
| Errore di provisioning | La macchina virtuale non è disponibile a causa di problemi di provisioning imprevisti. Il provisioning della macchina virtuale non è riuscito a causa di un errore imprevisto |
| Migrazione in tempo reale | La macchina virtuale è sospesa a causa di un'operazione di migrazione in tempo reale per il mantenimento della memoria. In genere, la macchina virtuale riprende entro 10 secondi. Al momento non è richiesta alcuna azione aggiuntiva |
| Migrazione in tempo reale | La macchina virtuale è sospesa a causa di un'operazione di migrazione in tempo reale per il mantenimento della memoria. In genere, la macchina virtuale riprende entro 10 secondi. Al momento non è richiesta alcuna azione aggiuntiva | 
| Disco remoto disconnesso | La macchina virtuale non è disponibile a causa di una perdita di connettività al disco remoto. Microsoft sta lavorando per ristabilire la connettività del disco. Al momento non è richiesta alcuna azione aggiuntiva |
| Problema del servizio di Azure | Il problema del servizio di Azure è influenzato dalla macchina virtuale |
| Problema di rete | Questa macchina virtuale è interessata da un dispositivo di rete Top-of-rack |
| Non disponibile | La macchina virtuale non è disponibile. Al momento non è possibile determinare il motivo del tempo di inattività |
| Riavvio del server host | La macchina virtuale non è disponibile a causa di un riavvio imprevisto del server host. Un problema imprevisto del server host impedisce il ripristino automatico della macchina virtuale |
| Ridistribuzione a causa di un errore dell'host | La macchina virtuale non è disponibile a causa di un errore imprevisto nel server host. Un problema imprevisto relativo all'host impedisce il ripristino automatico della macchina virtuale |
| Errore host imprevisto | La macchina virtuale non è disponibile a causa di un errore imprevisto nel server host. Un problema imprevisto relativo all'host impedisce il ripristino automatico della macchina virtuale |
| Ridistribuzione a causa di una manutenzione non pianificata dell'host | La macchina virtuale non è disponibile a causa di un errore imprevisto nel server host. Un problema imprevisto relativo all'host impedisce il ripristino automatico della macchina virtuale |
| Errore di provisioning | La macchina virtuale non è disponibile a causa di problemi di provisioning imprevisti. Il provisioning della macchina virtuale non è riuscito a causa di un errore imprevisto |
| Disco remoto disconnesso | La macchina virtuale non è disponibile a causa di una perdita di connettività al disco remoto. Un problema imprevisto impedisce il ripristino automatico della macchina virtuale |
| Riavvio causato dall'aggiornamento del sistema operativo guest | Un riavvio è stato avviato dalla piattaforma Azure per applicare un nuovo aggiornamento del sistema operativo guest. La macchina virtuale sarà nuovamente online al termine del riavvio |
