<properties
   pageTitle="Provider di calcolo,rete e archiviazione in Gestione risorse di Microsoft Azure"
   description="Panoramica concettuale sui provider di calcolo, rete e risorse di archiviazione (CRP NRP e SRP)"
   services="virtual-machines"
   documentationCenter="dev-center-name"
   authors="mahthi"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="mahthi"/>

# Provider di calcolo, rete e di archiviazione in Gestione risorse di Microsoft Azure

L'inclusione delle funzionalità di calcolo, rete e archiviazione con Gestione risorse di Microsoft Azure sostanzialmente consentirà di semplificare la distribuzione e la gestione di complesse applicazioni in esecuzione in IaaS. Molte applicazioni richiedono una combinazione di risorse, tra cui una rete virtuale, un account di archiviazione, una macchina virtuale e un'interfaccia di rete. Gestione risorse di Microsoft Azure offre la possibilità di creare un modello JSON per distribuire e gestire tutte queste risorse insieme come una singola applicazione.

## Vantaggi dell’integrazione del calcolo, della rete e delle risorse di archiviazione in Gestione risorse di Microsoft Azure

Gestione risorse di Microsoft Azure offre la possibilità di sfruttare facilmente i modelli di applicazione predefiniti o consente di creare un modello di applicazione per distribuire e gestire le risorse di calcolo, rete e archiviazione in Micorosft Azure. In questa sezione verranno esaminati i vantaggi della distribuzione delle risorse tramite Gestione risorse di Microsoft Azure.

-	Complessità resa più semplice: creazione, integrazione e collaborazione in applicazioni complesse che possono includere l'intera gamma di risorse di Microsoft Azure \(ad esempio siti Web, database SQL, macchine virtuali o reti virtuali\) da un file di modello condivisibile
-	Flessibilità di disporre distribuzioni ripetibili per amministratori di sistema, reparti di sviluppo e DevOps quando si utilizza lo stesso file di modello
-	Una stretta integrazione delle estensioni di macchina virtuale \(script personalizzato, DSC, Chef, Puppet e così via\) con Gestione risorse di Microsoft Azure in un file di modello consente una facile orchestrazione della configurazione interna alle macchine virtuali
-	Definizione di tag e propagazione della fatturazione di tali tag per le risorse di calcolo, rete e archiviazione
-	Gestione degli accessi semplice e precisa dell'organizzazione mediante il controllo degli accessi in base al ruolo di Microsoft Azure
-	Aggiornamento semplificato mediante modifica del modello originale e successiva distribuzione dello stesso


## Miglioramenti delle API di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure

Oltre ai vantaggi indicati in precedenza, esistono alcuni miglioramenti significativi delle prestazioni nelle API rilasciate.

-	Abilitazione della distribuzione di macchine virtuali di grandi dimensioni e parallele
-	Supporto per tre domini di errore nei set di disponibilità
-	Estensione di uno script personalizzato migliorato che consente di specificare gli script da un URL personalizzato accessibile pubblicamente
- Integrazione delle macchine virtuali con l'insieme di credenziali chiave di Microsoft Azure per l'archiviazione altamente protetta e la distribuzione privata dei segreti da [moduli di protezione hardware](http://wikipedia.org/wiki/FIPS_140-2) [convalidati con FIPS](http://wikipedia.org/wiki/Hardware_security_module)
-	Fornisce i blocchi di creazione di base delle reti tramite le API per consentire ai clienti di creare applicazioni complesse che includano interfacce di rete, servizi di bilanciamento del carico e reti virtuali
-	Interfacce di rete come un nuovo oggetto consente di subire e riutilizzare una configurazione di rete complessa per macchine virtuali
-	Servizi di bilanciamento del carico come una risorsa di prima classe consente assegnazioni di indirizzi IP
-	Le API di rete virtuale granulari consentono di semplificare la gestione di reti virtuali singole

## Differenze concettuali con l'introduzione di nuove API

In questa sezione esamineremo alcune delle più importanti differenze concettuali tra le API basate su codice XML API disponibili oggi e le API basate su JSON API disponibili tramite Gestione risorse di Microsot Azure per calcolo, rete e archiviazione.

 Item | Gestione servizi di Microsoft Azure basata su XML | Provider di calcolo, rete e di archiviazione basati su JSON
 ---|---|---
| Servizio cloud per macchine virtuali |	Servizio cloud era un contenitore per mantenere le macchine virtuali che richiedevano disponibilità dalla piattaforma e bilanciamento del carico. | Servizio cloud non è più un oggetto richiesto per la creazione di una macchina virtuale che utilizza il nuovo modello. |
| SET DI DISPONIBILITÀ | La disponibilità per la piattaforma era indicata mediante la configurazione  dello stesso oggetto "AvailabilitySetName" nelle macchine virtuali. Il numero massimo di domini di errore era 2. | Il set di disponibilità è una risorsa esposta dal provider Microsoft.Compute. Le macchine virtuali che richiedono un'elevata disponibilità devono essere incluse nel set di disponibilità. Il numero massimo di domini di errore è ora 3. |
| Gruppi di affinità |	I gruppi di affinità erano necessari per la creazione di reti virtuali. Tuttavia, con l'introduzione di reti virtuali regionali, tale requisito non era più necessario. |Per semplificare, non esiste il concetto di gruppi di affinità nelle API esposte tramite Gestione risorse di Microsoft Azure. |
| Bilanciamento del carico. | La creazione di un servizio cloud fornisce un servizio di bilanciamento del carico implicito per le macchine virtuali distribuite. | Il bilanciamento del carico è una risorsa esposta dal provider Microsoft.Network. L'interfaccia di rete primaria delle macchine virtuali che deve essere configurata con carico bilanciato deve fare riferimento al servizio di bilanciamento del carico. I servizi di bilanciamento del carico possono essere interni o esterni. [Per ulteriori informazioni.](resource-groups-networking.md) |
|Indirizzo IP virtuale | I servizi cloud otterranno un indirizzo predefinito \(indirizzo IP virtuale\) quando una macchina virtuale viene aggiunta a un servizio cloud. L'indirizzo IP virtuale è l'indirizzo associato al servizio di bilanciamento del carico implicito. | Il bilanciamento del carico è una risorsa esposta dal provider Microsoft.Network. L’indirizzo IP pubblico può essere statico \(riservato\) o dinamico. Gli indirizzi IP pubblici possono essere assegnati a un servizio di bilanciamento del carico. Gli indirizzi IP pubblici possono essere protetti tramite gruppi di protezione. |
|Indirizzo IP riservato|	In Microsoft Azure è possibile riservare un indirizzo IP e associarlo a un servizio cloud per assicurarsi che l'indirizzo IP sia permanente. | L’indirizzo IP pubblico può essere creato in modalità "statica" e offre le stesse funzionalità di un "indirizzo IP riservato". Gli indirizzi IP statici possono essere assegnati solo a un servizio di bilanciamento del carico al momento. |
|Indirizzo IP pubblico per macchina virtuale | Gli indirizzi IP pubblici possono inoltre essere associati direttamente a una macchina virtuale. | Il bilanciamento del carico è una risorsa esposta dal provider Microsoft.Network. L’indirizzo IP pubblico può essere statico \(riservato\) o dinamico. Tuttavia, al momento solo indirizzi IP pubblici dinamici possono essere assegnati a un'interfaccia di rete per ottenere un indirizzo IP pubblico per ogni macchina virtuale. |
|Endpoint| In una macchina virtuale era necessario configurare gli endpoint di input per  consentire connettività a determinate porte. Una delle modalità comuni di connessione alle macchine virtuali eseguita mediante l'impostazione di endpoint di input. | È possibile configurare le regole NAT in ingresso in servizi di bilanciamento del carico per ottenere le stesse funzionalità di abilitazione degli endpoint su porte specifiche per la connessione alle macchine virtuali. |
|Nome DNS| Un servizio cloud otterrebbe un nome DNS univoco globale implicito. Ad esempio: `mycoffeeshop.cloudapp.net`. | I nomi DNS sono parametri facoltativi che possono essere specificati in una risorsa di indirizzo IP pubblico. Il nome di dominio completo avrà il seguente formato...`<domainlabel>.<region>.cloudapp.azure.com` |
|Interfacce di rete | Interfacce di rete primarie e secondarie e le relative proprietà sono state definite come configurazione di rete di una macchina virtuale. | L’interfaccia di rete è una risorsa esposta dal provider Microsoft.Network. Il ciclo di vita dell'interfaccia di rete non è correlato a una macchina virtuale. |

## Introduzione a modelli di Microsoft Azure per macchine virtuali

È possibile iniziare a utilizzare i modelli di Microsoft Azure sfruttando i vari strumenti disponibili per lo sviluppo e distribuzione per la piattaforma.

### Portale di Azure

Il portale di Microsoft Azure continuerà a offrire la possibilità di distribuire macchine virtuali e macchine virtuali di anteprima contemporaneamente. Il portale di Microsoft Azure consentirà inoltre le distribuzioni del modello personalizzato.

### Azure PowerShell

Azure PowerShell avrà due modalità di distribuzione: la modalità **AzureServiceManagement** e  la modalità **AzureResourceManager**. La modalità di AzureResourceManager conterrà anche i cmdlet per gestire macchine virtuali, reti virtuali e account di archiviazione. Per altre informazioni, leggere [qui](powershell-azure-resource-manager.md).

### Interfaccia della riga di comando di Azure

L’interfaccia della riga di comando di Microsoft Azure avrà due modalità di distribuzione: la modalità **AzureServiceManagement** e  la modalità **AzureResourceManager**. La modalità di AzureResourceManager conterrà inoltre i cmdlet per gestire macchine virtuali, reti virtuali e account di archiviazione. Per altre informazioni, leggere [qui](xplat-cli-azure-resource-manager.md).

### Visual Studio

Con l'ultima versione di Azure SDK per Visual Studio, è possibile creare e distribuire macchine virtuali e applicazioni complesse direttamente da Visual Studio. Visual Studio offre la possibilità di eseguire la distribuzione da un elenco di modelli predefinito o avviare da un modello vuoto.

### API REST

La documentazione dettagliata sull’API REST per i provider di calcolo, rete e risorse di archiviazione è disponibile [qui](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Domande frequenti

**È possibile creare una macchina virtuale mediante la nuova funzionalità Gestione risorse di Azure per distribuire in una rete virtuale o un account di archiviazione creato utilizzando le API di gestione del servizio di Microsoft Azure?**

La funzionalità non è supportata al momento. Non è possibile eseguire la distribuzione mediante le API della nuova funzionalità Gestione risorse di Microsoft Azure per distribuire una rete virtuale o un account di archiviazione creato utilizzando le API di gestione del servizio di Microsoft Azure.

**È possibile creare una macchina virtuale mediante le API della nuova funzionalità Gestione risorse di Microsoft con un’immagine utente creata mediante le API di gestione dei servizi Microsoft Azure?**

La funzionalità non è supportata al momento. Tuttavia, è possibile copiare i file VHD da un account di archiviazione creato mediante le API di gestione del servizio e copiarlo in un nuovo account creato mediante le nuove API di gestione risorse di Microsoft Azure.

**Quali sono le conseguenze sulla quota della mia sottoscrizione?**

Le quote per le macchine virtuali, reti virtuali e gli account di archiviazione creati tramite le nuove API di gestione risorse di Microsoft Azure sono separate dalle quote attualmente disponibili. Ogni sottoscrizione ottiene nuove quote per creare le risorse mediante le nuove API. Per ulteriori informazioni sulle quote aggiuntive, fare clic [qui](azure-subscription-service-limits.md).

**È possibile continuare a utilizzare gli script automatizzati per il provisioning di macchine virtuali, reti virtuali, account di archiviazione e altrotramite le nuove API di Gestione risorse di Microsoft Azure?**

Tutti gli script e le automazioni creati continueranno a funzionare per le macchine virtuali e le reti virtuali esistenti creati con la modalità di gestione dei servizi Microsoft Azure. Tuttavia, è necessario aggiornare gli script per utilizzare il nuovo schema per la creazione delle stesse risorse tramite la nuova modalità di Gestione risorse di Microsoft Azure. Ulteriori informazioni su come modificare i propri[script per l’interfaccia della riga di comando di Microsoft Azure](xplat-cli-azure-manage-vm-asm-arm.md).

**Le reti virtuali create mediante le nuove API di Gestione risorse di Microsoft Azure possono essere connesse al circuito Express Route in uso?**

La funzionalità non è supportata al momento. Non è possibile connettere le reti virtuali create mediante le nuove API di Gestione risorse di Microsoft Azure con un circuito Express Route in uso. La funzionalità sarà supportata in futuro.

<!--HONumber=52-->
