<properties 
   pageTitle="Guida al supporto e ai criteri di ritiro per il sistema operativo guest di Azure | Azure" 
   description="Fornisce informazioni sul supporto che verrà fornito da Microsoft riguardo al sistema operativo guest di Azure usato da Servizi cloud." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="05/19/2015"
   ms.author="adegeo"/>

# Criteri relativi al supporto e al ritiro del sistema operativo guest di Azure
Le informazioni di questa pagina si riferiscono al sistema operativo guest di Azure ([sistema operativo guest](https://msdn.microsoft.com/library/azure/ff729422.aspx)) per i ruoli di lavoro e Web di Servizi cloud (PaaS). Non si applica alle macchine virtuali (IaaS).

Microsoft ha pubblicato i [criteri di supporto per il sistema operativo guest](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Questa pagina contiene informazioni dettagliate sul modo in cui i criteri sono implementati.

I criteri sono i seguenti:

1. Microsoft supporterà **almeno le ultime due famiglie del sistema operativo guest**. Quando una famiglia viene ritirata, i clienti hanno a disposizione 12 mesi dalla data ufficiale di ritiro per eseguire l'aggiornamento a una famiglia di sistemi operativi guest supportata più recente.
2. Microsoft supporterà **almeno le ultime due versioni delle famiglie del sistema operativo guest supportate**. 
3. Microsoft supporterà **almeno le ultime due versioni di Azure SDK**. Quando una versione di SDK viene ritirata, i clienti avranno 12 mesi dalla data di ritiro ufficiale per eseguire l'aggiornamento a una versione più recente. 

Talvolta è possibile che siano supportati più di due rilasci o famiglie. Le informazioni di supporto ufficiale del sistema operativo guest saranno visualizzate nella [matrice di compatibilità di SDK e delle versioni del sistema operativo guest di Azure](cloud-services-guestos-update-matrix.md).


## Ritiro di una famiglia o una versione del sistema operativo guest 


Una nuova **famiglia** di sistemi operativi guest viene introdotta a un certo punto dopo il rilascio di una nuova versione ufficiale del sistema operativo Windows Server. Ogni volta che viene introdotta una nuova famiglia di sistemi operativi guest, Microsoft ritirerà la famiglia precedente.

Nuove **versioni** del sistema operativo guest vengono rilasciate quasi ogni mese per incorporare gli ultimi aggiornamenti di MSRC. A causa degli aggiornamenti regolari mensili, una versione del sistema operativo guest viene in genere disabilitata 60 giorni dopo il relativo rilascio. In questo modo sono disponibili per l'uso almeno due versioni del sistema operativo guest per ogni famiglia.

### Procedura di ritiro della famiglia di sistemi operativi guest 


Una volta annunciato il ritiro, i clienti hanno a disposizione un periodo di transizione di 12 mesi prima che la famiglia precedente venga ufficialmente rimossa dal servizio. Questo periodo di transizione può essere esteso a discrezione di Microsoft. Gli aggiornamenti vengono pubblicati nella [matrice di compatibilità di SDK e delle versioni del sistema operativo guest di Azure](cloud-services-guestos-update-matrix.md).

Dopo 6 mesi dall'inizio del periodo di transizione comincerà un processo di ritiro graduale. In questo periodo di tempo:

1. Microsoft informerà i clienti del ritiro. 
2. La versione più recente di Azure SDK non supporterà la famiglia di sistemi operativi guest ritirata.
3. Non sarà possibile eseguire nuove distribuzioni e ridistribuzioni di servizi cloud per la famiglia ritirata

Microsoft continuerà a rilasciare la nuova versione del sistema operativo guest che incorpora gli ultimi aggiornamenti di MSRC fino all'ultimo giorno del periodo di transizione, noto come "data di scadenza". A quel punto, tutti i servizi cloud ancora in esecuzione non saranno supportati in base al contratto di servizio di Azure. Trascorsa tale data, Microsoft potrà forzare l'aggiornamento, l'eliminazione o l'interruzione dei servizi.



### Processo durante il ritiro della versione del sistema operativo guest 
Se i clienti hanno impostato l'aggiornamento automatico del sistema operativo guest in uso, non è necessario preoccuparsi delle versioni del sistema operativo. Viene usata sempre la versione più recente del sistema operativo guest.

Le versioni del sistema operativo guest vengono rilasciate ogni mese. A causa della frequenza dei rilasci regolari, ogni versione ha una durata fissa.

Allo scadere del periodo di 60 giorni una versione viene "*disabilitata*". "Disabilitata" significa che la versione viene rimossa dal portale di gestione di Azure. Inoltre non può più essere impostata dal file di configurazione CSCFG. Le distribuzioni esistenti vengono mantenute in esecuzione, ma non è consentito eseguire nuove distribuzioni e aggiornamenti di configurazione e di codice alle distribuzioni esistenti.

In un momento successivo, la versione del sistema operativo guest "*scade*", e viene forzato l'aggiornamento delle installazioni che ancora eseguono la versione in questione e viene impostato l'aggiornamento automatico del sistema operativo Guest per il futuro. La scadenza è un processo graduale, per cui il periodo che intercorre tra la disabilitazione e la scadenza può variare.

Questi periodi possono essere resi più lunghi a discrezione di Microsoft per facilitare le transizioni del cliente. Le modifiche vengono comunicate nella [matrice di compatibilità di SDK e delle versioni del sistema operativo guest di Azure](cloud-services-guestos-update-matrix.md).



### Notifiche durante il ritiro 

* **Ritiro della famiglia** <br>Microsoft userà i post di blog e la notifica tramite il portale di gestione. Gli utenti che usano ancora una famiglia di sistemi operativi guest ritirata verranno informati tramite una comunicazione diretta (e-mail, messaggi sul portale, telefonata) agli amministratori del servizio assegnato. Tutte le modifiche verranno inserite in questa pagina e il feed RSS verrà elencato all'inizio di questa pagina. 


* **Ritiro della versione** <br>Tutte le modifiche verranno inserite in questa pagina e il feed RSS verrà elencato all'inizio di questa pagina, inclusa la data di rilascio, disabilitazione e scadenza. Se sono presenti distribuzioni in esecuzione su una versione o famiglia di sistemi operativi guest disabilitata, verranno inviati appositi messaggi e-mail agli amministratori dei servizi. La tempistica di questi messaggi può variare. In genere vengono inviati almeno un mese prima della disabilitazione, anche se questo non rappresenta un impegno contrattuale di servizio.


## Domande frequenti

**In che modo è possibile ridurre gli effetti della migrazione?**

Per progettare i Servizi cloud è consigliabile usare la famiglia di sistemi operativi guest più recente.

1. Iniziare prima a pianificare la migrazione a una famiglia più recente. 
2. Configurare distribuzioni di prova temporanee per testare il servizio cloud in esecuzione nella nuova famiglia. 
3. [Impostare la versione del sistema operativo guest](https://msdn.microsoft.com/library/azure/gg433101.aspx) su "Automatica" (osVersion=* nel file [.CSCFG](https://msdn.microsoft.com/library/azure/gg456324.aspx)) in modo che la migrazione alle nuove versioni del sistema operativo guest venga eseguita automaticamente.

**Che cosa accade se per l'applicazione Web è necessaria una maggiore integrazione con il sistema operativo?**

Se per l'architettura dell'applicazione Web è richiesta una dipendenza maggiore dal sistema operativo sottostante, usare le funzionalità supportate della piattaforma, ad esempio le [attività di avvio](https://msdn.microsoft.com/library/windowsazure/gg456327.aspx) o altri meccanismi di estendibilità eventualmente disponibili in futuro. In alternativa, è anche possibile usare [Macchine virtuali di Azure](http://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS, Infrastructure as a Service), in cui si è responsabili della manutenzione del sistema operativo sottostante.
 

<!---HONumber=August15_HO6-->