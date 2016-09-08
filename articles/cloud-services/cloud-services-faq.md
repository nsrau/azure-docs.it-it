<properties
	pageTitle="Domande frequenti sui servizi cloud | Microsoft Azure"
	description="Domande frequenti sui servizi cloud."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="adegeo"/>

# Domande frequenti sui servizi cloud
Questo articolo risponde ad alcune domande frequenti sui servizi cloud di Microsoft Azure. Per informazioni generali sui prezzi e sul supporto di Azure, vedere [Domande frequenti sul supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083). Per informazioni sulle dimensioni, vedere la pagina [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md).

## Certificati

### Non è possibile rimuovere un certificato scaduto

Azure impedisce la rimozione di un certificato mentre viene usato. È necessario eliminare la distribuzione che fa uso del certificato o aggiornarla con un certificato diverso o rinnovato.

### Eliminare un certificato scaduto

Se il certificato non viene usato, è possibile rimuoverlo mediante il cmdlet [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) di PowerShell.

### Sono presenti certificati scaduti denominati Gestione dei servizi Microsoft Azure per le estensioni

Questi certificati vengono creati ogni volta che si aggiunge un'estensione al servizio cloud, ad esempio l'estensione Desktop remoto, e vengono usati unicamente per crittografare e decrittografare la configurazione privata dell'estensione. Non è un problema se questi certificati scadono, perché la data di scadenza non viene controllata.

### I certificati eliminati continuano a riapparire

Molto probabilmente questi certificati continuano a essere eliminati a causa di uno strumento che si sta usando, ad esempio Visual Studio. Ogni volta che ci si riconnette con uno strumento che usa un certificato, questo viene caricato nuovamente in Azure.

### I certificati continuano a scomparire

Durante il riciclo dell'istanza di macchina virtuale vengono perse tutte le modifiche locali. Usare un'[attività di avvio](cloud-services-startup-tasks.md) per installare i certificati nella macchina virtuale ogni volta che viene avviato il ruolo.

### Dove deve essere installato il certificato?

**My** Certificato dell'applicazione con chiave privata, con estensioni pfx e p12.

**CA** Tutti i certificati intermedi, come CA secondari e criteri, vanno in questo archivio.

**ROOT** Archivio CA radice in cui inserire il certificato CA radice principale.

## Risoluzione dei problemi

### Non è possibile riservare un indirizzo IP in un servizio cloud con più indirizzi VIP

Assicurarsi prima di tutto che l'istanza di macchina virtuale per cui si sta provando a riservare l'indirizzo IP sia accesa. Assicurarsi quindi che vengano usati indirizzi IP riservati sia per la distribuzione di staging che per quella di produzione. **Non** modificare le impostazioni durante l'aggiornamento della distribuzione.

<!---HONumber=AcomDC_0824_2016-->