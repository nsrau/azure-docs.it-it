---
title: Domande frequenti relative ai problemi di configurazione e gestione per Servizi cloud di Microsoft Azure| Microsoft Docs
description: Questo articolo elenca le domande frequenti relative alla configurazione e alla gestione per Servizi cloud di Microsoft Azure.
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: genli
ms.openlocfilehash: 7a1dc5d755303d12aa070308a19ab81ef2b911fb
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemi di configurazione e gestione per Servizi cloud di Azure: domande frequenti

Questo articolo include le domande frequenti relative ai problemi di configurazione e gestione per [Servizi cloud di Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Per informazioni sulle dimensioni, vedere la pagina [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificati**

- [Perché è incompleto la catena di certificati del certificato SSL del servizio Cloud in uso?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Che cos'è lo scopo di "Windows Azure estensioni degli strumenti di crittografia certificato per"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Come è possibile generare una firma richiesta certificato (CSR) senza "RDP ing" all'istanza?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Il certificato di gestione del servizio Cloud sta per scadere. Come rinnovare la sottoscrizione?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Come è possibile automatizzare l'installazione del principale certificate(.pfx) SSL e certificate(.p7b) intermedio?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)

**Monitoraggio e registrazione**

- [Quali sono le funzionalità future del servizio Cloud nel portale di Azure che consente di gestione e monitorare le applicazioni?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Perché IIS interrompere la scrittura nella directory log?](#why-does-iis-stop-writing-to-the-log-directory)

**Network configuration**

- [Impostazione di timeout di inattività di bilanciamento del carico di Azure](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Come associare un indirizzo IP statico per il servizio Cloud?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Quali sono le caratteristiche e funzionalità che fornisce gli indirizzi IP di base Azure o gli ID e DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Come abilitare HTTP/2 nella macchina virtuale di servizi Cloud?](#how-to-enable-http2-on-cloud-services-vm)

**Autorizzazioni**

- [È possibile desktop remoto interno tecnici di Microsoft per le istanze del servizio Cloud senza autorizzazione.](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Desktop remoto alla macchina virtuale del servizio Cloud non è possibile usare il file RDP. Controllabili il seguente errore: si è verificato un errore di autenticazione (codice: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Ridimensionamento**

- [Impossibile ridimensionare oltre X istanze](#i-cannot-scale-beyond-x-instances)
- [Come è possibile configurare in base alle metriche memoria scalabilità automatica?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Generico**

- [Come è possibile aggiungere "nosniff" per il sito Web?](#how-do-i-add-nosniff-to-my-website)
- [Personalizzazione di IIS per un ruolo web](#how-do-i-customize-iis-for-a-web-role)
- [Che cos'è il limite di quota per il servizio Cloud?](#what-is-the-quota-limit-for-my-cloud-service)
- [Perché l'unità nella macchina virtuale del servizio Cloud visualizzato poco spazio libero su disco?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Come è possibile aggiungere un'estensione Antimalware per i servizi Cloud in modo automatico?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Come abilitare l'indicazione nome Server (SNI) per i servizi Cloud?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Come è possibile aggiungere tag al servizio Cloud Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Il portale di Azure non visualizza la versione del SDK del servizio Cloud. Come è possibile ottenere che?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Si desidera arrestare il servizio Cloud da diversi mesi. Come è possibile ridurre i costi di fatturazione del servizio Cloud senza perdere l'indirizzo IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certificati

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Perché è incompleto la catena di certificati del certificato SSL del servizio Cloud in uso?
    
È consigliabile installare la catena di certificati completa (certificato foglia, certificati intermedi e certificato radice) invece che solo il certificato foglia. Quando si installa solo il certificato foglia, ci si basa su Windows per creare la catena di certificati scorrendo l'elenco di scopi consentiti. In caso di problemi intermittenti di rete o DNS in Azure o Windows Update quando Windows tenta di convalidare il certificato, il certificato potrebbe essere considerato non valido. Installando la catena di certificati completa, è possibile evitare questo problema. Il post di blog [How to install a chained SSL Certificate](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) (Come installare un certificato SSL incluso in una catena) spiega come fare.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Qual è lo scopo del certificato di crittografia degli strumenti di Microsoft Azure per le estensioni?

Questi certificati vengono creati automaticamente ogni volta che si aggiunge un'estensione per il servizio Cloud. In genere, si tratta dell'estensione WAD o RDP, ma può trattarsi anche di altre estensioni, ad esempio l'estensione antimalware o dell'agente di raccolta log. Questi certificati vengono usati unicamente per crittografare e decrittografare la configurazione privata dell'estensione. La data di scadenza non viene mai controllata, quindi non è importante se il certificato è scaduto. 

È possibile ignorare questi certificati. Per eseguire la pulizia dei certificati, provare a eliminarli tutti. Se si tenta di eliminare un certificato in uso, Azure genera un errore.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Come è possibile generare una richiesta di firma del certificato senza usare RPD per l'istanza?

Per indicazioni, vedere il documento seguente:

[Obtaining a certificate for use with Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/) (Ottenere un certificato per l'uso con Siti Web di Azure)

Il rappresentante del servizio è un file di testo. Che è necessario essere creato dal computer in cui il certificato verrà utilizzato. Anche se questo documento è scritto per un servizio app, la creazione della richiesta di firma del certificato è generica e si applica anche a Servizi cloud.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Il certificato di gestione del servizio Cloud sta per scadere. Come rinnovare la sottoscrizione?

È possibile utilizzare seguenti comandi di PowerShell per il rinnovo dei certificati di gestione:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

Il **Get-AzurePublishSettingsFile** creerà un nuovo certificato di gestione in **sottoscrizione** > **i certificati di gestione** nel portale di Azure. Il nome del nuovo certificato è simile a "YourSubscriptionNam]-[CurrentDate] - credenziali".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Come è possibile automatizzare l'installazione del principale certificate(.pfx) SSL e certificate(.p7b) intermedio?

È possibile automatizzare questa attività utilizzando uno script di avvio (cmd/batch/PowerShell) e registrare lo script di avvio nel file di definizione del servizio. Aggiungere uno script di avvio sia il certificato (file con estensione p7b) nella cartella del progetto della stessa directory dello script di avvio.

Per altre informazioni, vedere gli articoli seguenti:
- [Come configurare ed eseguire attività di avvio per un servizio Cloud](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks)
- [Attività comuni di avvio del servizio Cloud](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Quali sono le funzionalità future del servizio Cloud nel portale di Azure che consente di gestione e monitorare le applicazioni?

Sarà presto disponibile la possibilità di generare un nuovo certificato per Remote Desktop Protocol (RDP). In alternativa, è possibile eseguire lo script seguente:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Sarà disponibile a breve la possibilità di scegliere BLOB o locale come percorso di caricamento dei file con estensione csdef e cscfg. Usare [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0) per impostare il valore di ciascuna posizione.

Possibilità di monitorare le metriche a livello di istanza. In [Come monitorare i servizi cloud](cloud-services-how-to-monitor.md) sono disponibili funzionalità di monitoraggio aggiuntive.

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Perché IIS smette di scrivere nella directory dei log?
È stata superata la quota di archiviazione locale per la scrittura nella directory dei log. Per risolvere il problema, è possibile eseguire una delle tre operazioni seguenti:
* Abilitare la diagnostica per IIS e spostare periodicamente la diagnostica nell'archiviazione BLOB.
* Rimuovere manualmente i file di log dalla directory di registrazione.
* Aumentare il limite di quota per le risorse locali.

Per altre informazioni, vedere i documenti seguenti:
* [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Log di IIS interrompere la scrittura nel servizio Cloud](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="network-configuration"></a>Network configuration

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Come si imposta il timeout di inattività per Azure Load Balancer?
È possibile specificare il timeout nel file di definizione del servizio (con estensione csdef) analogo al seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Per altre informazioni, vedere [New: Configurable Idle Timeout for Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) (Novità: Timeout di inattività configurabile per Azure Load Balancer).

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Come associare un indirizzo IP statico per il servizio Cloud?
Per configurare un indirizzo IP statico, è necessario creare un IP riservato. Questo indirizzo IP riservato può essere associato a un nuovo servizio Cloud o a una distribuzione esistente. Vedere i documenti seguenti per informazioni dettagliate:
* [Come creare un indirizzo IP riservato](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Riservare l'indirizzo IP di un servizio Cloud esistente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associare un indirizzo IP riservato per un nuovo servizio Cloud](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associare un indirizzo IP riservato a una distribuzione in esecuzione](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associare un indirizzo IP riservato a un servizio Cloud, utilizzare un file di configurazione del servizio](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quali sono le caratteristiche e le funzionalità fornite dai sistemi di base di rilevamento e prevenzione delle intrusioni e di prevenzione degli attacchi DDoS di Azure?
Azure offre sistemi di rilevamento e prevenzione delle intrusioni nei server fisici dei data center per consentire la difesa dalle minacce. I clienti possono anche distribuire soluzioni di sicurezza di terze parti, ad esempio web application firewall, firewall di rete, antimalware, sistemi di rilevamento delle intrusioni, sistemi di prevenzione e altro ancora. Per altre informazioni, vedere [Protect your data and assets and comply with global security standards](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity) (Proteggere dati e asset e rispettare gli standard di sicurezza globali).

Microsoft esegue un monitoraggio continuo di server, reti e applicazioni per rilevare le minacce. L'approccio di gestione delle minacce su più fronti adottato in Azure prevede l'uso di funzionalità di rilevamento delle intrusioni, prevenzione contro attacchi Distributed Denial of Service (DDoS), test di penetrazione, analisi comportamentali, rilevamento di anomalie e apprendimento automatico al fine di rafforzare continuamente le difese e ridurre i rischi. Microsoft Antimalware per Azure protegge i servizi Cloud di Azure e macchine virtuali. In aggiunta è possibile distribuire soluzioni di terze parti, come web application firewall, firewall di rete, antimalware, sistemi di rilevamento e prevenzione delle intrusioni e altro ancora.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Come è possibile abilitare HTTP/2 nella macchina virtuale di Servizi cloud di Microsoft Azure?

Windows 10 e Windows Server 2016 includono il supporto per HTTP/2 sia sul lato client sia sul lato server. Se il client (browser) si connette al server IIS mediante il protocollo TLS che negozia HTTP/2 tramite le estensioni TLS, non è necessario apportare modifiche sul lato server. Con il protocollo TLS infatti come impostazione predefinita viene inviata l'intestazione h2-14 che specifica l'utilizzo di HTTP/2. Se invece il client invia un'intestazione di aggiornamento per eseguire l'aggiornamento a HTTP/2, è necessario apportare la modifica di seguito sul lato server per garantire il funzionamento dell'aggiornamento e la disponibilità di una connessione HTTP/2. 

1. Eseguire regedit.exe.
2. Individuare la chiave del Registro di sistema: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Creare un nuovo valore DWORD denominato **DuoEnabled**.
4. Impostare il relativo valore su 1.
5. Riavviare il server.
6. Passare al **sito Web predefinito** e in **Associazioni** creare una nuova associazione TLS con il certificato autofirmato appena creato. 

Per altre informazioni, vedere:

- [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 in IIS)
- [Video: HTTP/2 in Windows 10: Browser, Apps and Web Server](https://channel9.msdn.com/Events/Build/2015/3-88) (Video: HTTP/2 in Windows 10: Browser, app e server Web)
         

Questi passaggi può essere automatizzati tramite un'attività di avvio, in modo che ogni volta che viene creata una nuova istanza PaaS, è possibile eseguire le modifiche in precedenza nel Registro di sistema. Per ulteriori informazioni, vedere [come configurare ed eseguire attività di avvio per un servizio Cloud](cloud-services-startup-tasks.md).

 
Dopo questa operazione è possibile verificare l'abilitazione del protocollo HTTP/2 usando uno dei metodi seguenti:

- Abilitare la versione del protocollo nei log di IIS ed esaminare i log di IIS. HTTP/2 verrà indicato nei log. 
- Abilitare lo strumento di sviluppo F12 in Internet Explorer o Edge e passare alla scheda di rete per controllare il protocollo. 

Per altre informazioni, vedere [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 in IIS).

## <a name="permissions"></a>Autorizzazioni

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Come si implementa l'accesso in base al ruolo per Servizi cloud?
Servizi cloud non supporta il modello di controllo degli accessi in base al ruolo, perché non è un servizio basato su Azure Resource Manager.

Vedere [Controllo degli accessi in base al ruolo di Azure e Amministratore sottoscrizione classico](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="remote-desktop"></a>Desktop remoto

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>È possibile desktop remoto interno tecnici di Microsoft per le istanze del servizio Cloud senza autorizzazione.
Microsoft segue un processo strict che non consente agli ingegneri interni di desktop remoto al servizio Cloud senza autorizzazione scritta (posta elettronica o altre comunicazioni scritte) dal proprietario o da loro designata.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Desktop remoto alla macchina virtuale del servizio Cloud non è possibile usare il file RDP. Controllabili il seguente errore: si è verificato un errore di autenticazione (codice: 0x80004005)

Questo errore può verificarsi se si utilizza il file RDP da un computer che fa parte di Azure Active Directory. Per risolvere questo problema, seguire questi passaggi:

1. Il file RDP scaricato e quindi scegliere **modifica**.
2. Aggiungere "&#92;" come prefisso prima il nome utente. Ad esempio, utilizzare **. \username.** anziché **username**.

## <a name="scaling"></a>Ridimensionamento

### <a name="i-cannot-scale-beyond-x-instances"></a>Impossibile eseguire la scalabilità per un numero di istanze superiore a X
La sottoscrizione di Azure presenta un limite al numero di memorie centrali che è possibile usare. Se vengono usate tutte le memorie centrali disponibili, la scalabilità non funziona. Ad esempio, se si dispone di un limite di 100 core, questo significa che potrebbero essersi 100 istanze di macchine virtuali A1 dimensioni per il servizio Cloud o istanze di macchine virtuali di dimensioni A2 50.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Come è possibile configurare in base alle metriche memoria scalabilità automatica?

Scalabilità automatica in base alle metriche di memoria per i servizi Cloud non è attualmente supportato. 

Per risolvere questo problema, è possibile utilizzare Application Insights. Scalabilità automatica supporta Application Insights come origine delle metriche e può essere ridimensionato il numero di istanze di ruolo in base alle metriche guest come "Memoria".  È necessario configurare Application Insights nel file di pacchetto del progetto servizio Cloud (*. cspkg) e abilitare l'estensione di diagnostica Azure per il servizio per implementare questo feat.

Per ulteriori informazioni su come utilizzare una metrica personalizzata tramite Application Insights per configurare automaticamente scala nei servizi Cloud, vedere [per iniziare con scalabilità automatica, metrica personalizzata in Azure](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)

Per ulteriori informazioni sull'integrazione di diagnostica di Azure con Application Insights per i servizi Cloud, vedere [inviare servizio Cloud, macchina virtuale o dell'infrastruttura del servizio dati di diagnostica ad Application Insights](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)

Per altre informazioni su come abilitare Application Insights per i servizi Cloud, vedere [Application Insights per servizi Cloud di Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Per ulteriori informazioni su come abilitare la registrazione diagnostica di Azure per i servizi Cloud, vedere [configurare diagnostica per servizi Cloud di Azure e macchine virtuali](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Generico

### <a name="how-do-i-add-nosniff-to-my-website"></a>Come si aggiunge "nosniff" al sito Web?
Per impedire che i client eseguano lo sniffing dei tipi MIME, aggiungere un'impostazione a *web.config*.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

È possibile aggiungerla anche come impostazione in IIS. Usare il comando seguente con l'articolo sulle [attività di avvio comuni](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Come si personalizza IIS per un ruolo Web?
Usare lo script di avvio di IIS dell'articolo sulle [attività di avvio comuni](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Che cos'è il limite di quota per il servizio Cloud?
Vedere [Limiti specifici del servizio](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Perché l'unità nella macchina virtuale del servizio Cloud visualizzato poco spazio libero su disco?
Questo comportamento è previsto e non dovrebbe causare alcun problema all'applicazione. Inserimento nel journal è attivata per l'unità di % % approot nelle macchine virtuali di Azure PaaS, che essenzialmente utilizza double la quantità di spazio che i file in genere occupano. Ci sono tuttavia alcuni aspetti da considerare che permettono di capire come questo non sia un vero problema.

Le dimensioni dell'unità %approot% vengono calcolate come <dimensioni del file con estensione cspkg + dimensioni massime del giornale di registrazione + margine di spazio libero> oppure 1,5 GB, a seconda di quale sia il valore maggiore. Le dimensioni della VM non sono rilevanti per questo calcolo. Le dimensioni della VM influiscono solo sulle dimensioni del disco C temporaneo. 

La scrittura nell'unità %approot% non è supportata. Se si scrive nella VM di Azure, è necessario farlo in una risorsa LocalStorage temporanea (o in un'altra posizione, ad esempio archiviazione BLOB, File di Azure e così via). La quantità di spazio disponibile nella cartella %approot% non è quindi significativa. Se non si è certi del fatto che l'applicazione scriva nell'unità %approot%, è possibile lasciare il servizio in esecuzione per alcuni giorni e quindi confrontare le dimensioni prima e dopo. 

Azure non scrive nell'unità %approot%. Una volta che il disco rigido virtuale viene creato dal file con estensione cspkg e montato nella VM di Azure, l'unica cosa che potrebbe scrivere in questa unità è l'applicazione. 

Le impostazioni del giornale di registrazione non sono configurabili e quindi non possono essere disattivate.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Come è possibile aggiungere un'estensione Antimalware per Servizi cloud in modo automatico?

È possibile abilitare l'estensione Antimalware usando lo script PowerShell nell'attività di avvio. Per l'implementazione seguire i passaggi descritti in questi articoli: 
 
- [Creare un'attività di avvio di PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Per altre informazioni sugli scenari di distribuzione dell'estensione Antimalware e sulla relativa abilitazione dal portale, vedere [Scenari di distribuzione di Antimalware](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Come è possibile abilitare Indicazione nome server (SNI) per Servizi cloud?

È possibile abilitare SNI in Servizi cloud usando uno dei metodi seguenti:

**Metodo 1: Utilizzo di PowerShell**

L'associazione SNI può essere configurata tramite il cmdlet PowerShell **New WebBinding** in un'attività di avvio per un'istanza del ruolo del servizio Cloud come indicato di seguito:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Come descritto [qui](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags potrebbe assumere uno dei valori seguenti:

|Valore|Significato|
------|------
|0|Nessuna indicazione nome server|
|1|Indicazione nome server abilitata |
|2 |Associazione non SNI che usa l'archivio certificati centrale|
|3|Associazione SNI che usa l'archivio certificati centrale |
 
**Metodo 2: Utilizzo di codice**

È possibile configurare l'associazione SNI anche tramite il codice nell'attività di avvio del ruolo, come descritto in questo [post di blog](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Usando uno qualsiasi degli approcci descritti in precedenza, è necessario che i rispettivi certificati (PFX) per nomi host specifici vengano installati prima nelle istanze dei ruoli usando un'attività di avvio o tramite il codice perché l'associazione SNI diventi efficace.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Come è possibile aggiungere tag al servizio cloud di Azure? 

Il servizio cloud è una risorsa classica. Solo le risorse create tramite Azure Resource Manager supportano i tag. Non è possibile applicare tag alle risorse classiche, ad esempio al servizio cloud. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Il portale di Azure non visualizza la versione del SDK del servizio Cloud. Come è possibile ottenere che?

Stiamo lavorando per fornire questa funzionalità nel portale di Azure. Nel frattempo, è possibile utilizzare seguenti comandi di PowerShell per ottenere la versione del SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Si desidera arrestare il servizio Cloud da diversi mesi. Come è possibile ridurre i costi di fatturazione del servizio Cloud senza perdere l'indirizzo IP?

Un servizio Cloud già distribuito Ottiene fatturato di calcolo e archiviazione Usa. Pertanto, anche se si arresta la macchina virtuale di Azure, verrà comunque ottenere addebitato per l'archiviazione. 

Ecco cosa è possibile eseguire per ridurre la fatturazione senza perdere l'indirizzo IP per il servizio:

1. [Riservare l'indirizzo IP](../virtual-network/virtual-networks-reserved-public-ip.md) prima di eliminare le distribuzioni.  Verrà addebitato solo per questo indirizzo IP. Per ulteriori informazioni sulla fatturazione di indirizzo IP, vedere [gli indirizzi IP prezzi](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Eliminare le distribuzioni. Non eliminare xxx.cloudapp.net, in modo che è possibile utilizzarlo per futuro.
3. Se si desidera ridistribuire il servizio Cloud con lo stesso di riserva per gli IP riservati nella sottoscrizione, vedere [indirizzi IP riservati per servizi Cloud e macchine virtuali](https://azure.microsoft.com/blog/reserved-ip-addresses/).

