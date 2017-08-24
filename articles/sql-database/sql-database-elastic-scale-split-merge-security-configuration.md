---
title: Configurazione della sicurezza del servizio di divisione e unione | Documentazione Microsoft
description: Impostazione dei certificati 409 per la crittografia
metakeywords: Elastic Database certificates security
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: f9e89c57-61a0-484f-b787-82dae2349cb6
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 7e6ccf51a4b75eef16a7df5c1a1018954af8e5dd
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017


---
# <a name="split-merge-security-configuration"></a>Configurazione della sicurezza del servizio di divisione e unione
Per usare il servizio di "split and merge", è necessario configurare correttamente le impostazioni sulla sicurezza. Il servizio rientra nella funzionalità Scalabilità elastica di database SQL di Microsoft Azur. Per altre informazioni, vedere [Esercitazione relativa allo strumento divisione-unione del database elastico](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configurazione dei certificati
I certificati vengono configurati in due modi. 

1. [Per configurare il certificato SSL](#to-configure-the-ssl-certificate)
2. [Per configurare i certificati client](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Per ottenere i certificati
È possibile ottenere i certificati da Autorità di certificazione (CA) pubbliche o dal [servizio certificati di Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Questi sono i metodi consigliati per ottenere i certificati.

Se tali opzioni non sono disponibili, è possibile generare **certificati autofirmati**.

## <a name="tools-to-generate-certificates"></a>Strumenti per generare i certificati
* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Per eseguire gli strumenti
* Da un Prompt dei comandi per gli sviluppatori per Visual Studio, vedere l'articolo relativo al [prompt dei comandi di Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 
  
    Se installato, passare a:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Ottenere il WDK da [Windows 8.1: download di kit e strumenti](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Per configurare il certificato SSL
Un certificato SSL è necessario per crittografare la comunicazione e autenticare il server. Scegliere il più appropriato dei tre seguenti scenari ed eseguirne tutti i passaggi:

### <a name="create-a-new-self-signed-certificate"></a>Creare un nuovo certificato autofirmato
1. [Creare un certificato autofirmato](#create-a-self-signed-certificate)
2. [Creare un file PFX per il certificato SSL autofirmato](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Caricare il certificato SSL nel servizio cloud](#upload-ssl-certificate-to-cloud-service)
4. [Aggiornare il certificato SSL nel file di configurazione del servizio](#update-ssl-certificate-in-service-configuration-file)
5. [Importare l'Autorità di certificazione SSL](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Per usare un certificato esistente dall'archivio certificati
1. [Esportare il certificato SSL dall'archivio certificati](#export-ssl-certificate-from-certificate-store)
2. [Caricare il certificato SSL nel servizio cloud](#upload-ssl-certificate-to-cloud-service)
3. [Aggiornare il certificato SSL nel file di configurazione del servizio](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Per usare un certificato esistente in un file con estensione pfx
1. [Caricare il certificato SSL nel servizio cloud](#upload-ssl-certificate-to-cloud-service)
2. [Aggiornare il certificato SSL nel file di configurazione del servizio](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Per configurare i certificati client
I certificati client sono necessari per autenticare le richieste al servizio. Scegliere il più appropriato dei tre seguenti scenari ed eseguirne tutti i passaggi:

### <a name="turn-off-client-certificates"></a>Disabilitare i certificati client
1. [Disabilitare l'autenticazione basata su certificati client](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Rilasciare nuovi certificati autofirmati
1. [Creare un'autorità di certificazione autofirmata](#create-a-self-signed-certification-authority)
2. [Caricare un certificato della CA nel servizio cloud](#upload-ca-certificate-to-cloud-service)
3. [Aggiornare il certificato della CA nel file di configurazione del servizio](#update-ca-certificate-in-service-configuration-file)
4. [Rilasciare certificati client](#issue-client-certificates)
5. [Creare file PFX per i certificati client](#create-pfx-files-for-client-certificates)
6. [Importare il certificato client](#Import-Client-Certificate)
7. [Copiare le identificazioni personali del certificato client](#copy-client-certificate-thumbprints)
8. [Configurare i client consentiti nel file di configurazione del servizio](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Usare i certificati client esistenti
1. [Find CA Public Key](#find-ca-public-key)
2. [Caricare un certificato della CA nel servizio cloud](#Upload-CA-certificate-to-cloud-service)
3. [Aggiornare il certificato della CA nel file di configurazione del servizio](#Update-CA-Certificate-in-Service-Configuration-File)
4. [Copiare le identificazioni personali del certificato client](#Copy-Client-Certificate-Thumbprints)
5. [Configurare i client consentiti nel file di configurazione del servizio](#configure-allowed-clients-in-the-service-configuration-file)
6. [Configurare il controllo della revoca del certificato client](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Indirizzi IP consentiti
L'accesso agli endpoint del servizio può essere limitato a intervalli specifici di indirizzi IP.

## <a name="to-configure-encryption-for-the-store"></a>Per configurare la crittografia per l'archivio
È necessario un certificato per crittografare le credenziali archiviate nell'archivio di metadati. Scegliere il più appropriato dei tre seguenti scenari ed eseguirne tutti i passaggi:

### <a name="use-a-new-self-signed-certificate"></a>Usare un nuovo certificato autofirmato
1. [Creare un certificato autofirmato](#create-a-self-signed-certificate)
2. [Creare un file PFX per il certificato di crittografia autofirmato](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Caricare il certificato di crittografia nel servizio cloud](#upload-encryption-certificate-to-cloud-service)
4. [Aggiornare il certificato di crittografia nel file di configurazione del servizio](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Usare un certificato esistente dall'archivio certificati
1. [Esportare il certificato di crittografia dall'archivio certificati](#export-encryption-certificate-from-certificate-store)
2. [Caricare il certificato di crittografia nel servizio cloud](#upload-encryption-certificate-to-cloud-service)
3. [Aggiornare il certificato di crittografia nel file di configurazione del servizio](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Usare un certificato esistente in un file PFX
1. [Caricare il certificato di crittografia nel servizio cloud](#upload-encryption-certificate-to-cloud-service)
2. [Aggiornare il certificato di crittografia nel file di configurazione del servizio](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Configurazione predefinita
La configurazione predefinita nega qualunque accesso all'endpoint HTTP. Questa è l'impostazione consigliata, in quanto le richieste inviate a tali endpoint posso includere dati sensibili come le credenziali di database.
La configurazione predefinita consente qualunque accesso all'endpoint HTTPS. Tale impostazione può essere limitata ulteriormente.

### <a name="changing-the-configuration"></a>Modifica della configurazione
Il gruppo di regole di controllo di accesso applicabili a un endpoint viene configurato nella sezione **<EndpointAcls>** del **file di configurazione del servizio**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Le regole incluse in un gruppo di controllo di accesso vengono configurate in una sezione <AccessControl name=""> del file di configurazione del servizio. 

Il formato è illustrato nella documentazione relativa agli elenchi di controllo di accesso di rete.
Ad esempio, per consentire l'accesso all'endpoint HTTPS solo per gli indirizzi IP compresi nell'intervallo da 100.100.0.0 a 100.100.255.255, le regole saranno simili alle seguenti:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Prevenzione di attacchi Denial of Service
Per rilevare e impedire attacchi Denial of Service sono supportati due diversi meccanismi:

* Limitare il numero di richieste simultanee per host remoto (opzione disattivata per impostazione predefinita).
* Limitare la frequenza di accesso per host remoto (opzione attivata per impostazione predefinita).

Questi meccanismi si basano sulle funzionalità illustrate più estesamente nella documentazione relativa alla sicurezza degli IP dinamici in IIS. Quando si modifica questa configurazione, prestare attenzione ai seguenti fattori:

* Comportamento del proxy e dei dispositivi NAT (Network Address Translation)rispetto alle informazioni sull'host remoto.
* Viene considerata ogni richiesta a qualsiasi risorsa nel ruolo Web (ad esempio, caricamento di script, immagini e così via).

## <a name="restricting-number-of-concurrent-accesses"></a>Limitazione del numero di accessi simultanei
Le impostazioni che configurano questo comportamento sono le seguenti:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Impostare DynamicIpRestrictionDenyByConcurrentRequests su true per abilitare questa protezione.

## <a name="restricting-rate-of-access"></a>Limitazione della frequenza di accesso
Le impostazioni che configurano questo comportamento sono le seguenti:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Configurazione della risposta a una richiesta negata
La seguente impostazione configura la risposta a una richiesta negata:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Per altri valori supportati, vedere la documentazione relativa alla sicurezza degli IP dinamici in IIS.

## <a name="operations-for-configuring-service-certificates"></a>Operazioni per la configurazione dei certificati di servizio
Questo argomento è solo per riferimento. Attenersi alla procedura di configurazione riportata in:

* Configurare il certificato SSL.
* Configurare i certificati client.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato
Eseguire:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Per personalizzare:

* -n con l'URL del servizio. Sono supportati caratteri jolly ("CN=*.cloudapp.net") e nomi alternativi ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net").
* -e con la data di scadenza del certificato creare una password complessa e specificarla quando richiesto.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Creare un file PFX per il certificato SSL autofirmato
Eseguire:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Immettere la password e quindi esportare il certificato con queste opzioni:

* Sì, esporta la chiave privata
* Esporta tutte le proprietà estese

## <a name="export-ssl-certificate-from-certificate-store"></a>Esportare il certificato SSL dall'archivio certificati
* Trovare il certificato.
* Fare clic su Azioni -> Tutte le attività -> Esporta.
* Esportare il certificato in un file PFX con queste opzioni:
  * Sì, esporta la chiave privata
  * Se possibile, includere tutti i certificati nel percorso della certificazione *Esporta tutte le proprietà estese

## <a name="upload-ssl-certificate-to-cloud-service"></a>Caricare il certificato SSL nel servizio cloud
Caricare il certificato con il file PFX esistente o generato con la coppia di chiavi SSL:

* Immettere la password che protegge le informazioni sulla chiave privata.

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Aggiornare il certificato SSL nel file di configurazione del servizio
Aggiornare il valore di identificazione personale della seguente impostazione nel file di configurazione del servizio con l'identificazione personale del certificato caricato nel servizio cloud:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importare l'Autorità di certificazione SSL
Seguire questa procedura in tutti gli account o i computer che comunicheranno con il servizio:

* Fare doppio clic sul file con estensione CER in Esplora risorse.
* Nella finestra di dialogo Certificato fare clic su Installa certificato.
* Importare il certificato nell'archivio delle Autorità di certificazione radice disponibili nell'elenco locale.

## <a name="turn-off-client-certificate-based-authentication"></a>Disabilitare l'autenticazione basata su certificati client
È supportata solo autenticazione basata su certificati client. Se viene disabilitata, consentirà l'accesso pubblico agli endpoint del servizio, a meno che siano implementati altri meccanismi (ad esempio, Rete virtuale di Microsoft Azure).

Per disabilitare la funzionalità, modificare queste impostazioni specificando false nel file di configurazione del servizio:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Copiare quindi la stessa identificazione personale del certificato SSL nell'impostazione del certificato della CA:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Creare un'autorità di certificazione autofirmata
Per creare un certificato autofirmato che funga da autorità di certificazione, seguire questa procedura:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Per personalizzarlo

* -e con la data di scadenza del certificato.

## <a name="find-ca-public-key"></a>Trovare la chiave pubblica CA
Tutti i certificati client devono essere rilasciati da un'autorità di certificazione considerata attendibile dal servizio. Trovare la chiave pubblica all'autorità di certificazione che ha rilasciato i certificati client da usare per l'autenticazione per caricarla nel servizio cloud.

Se il file con la chiave pubblica non è disponibile, esportarlo dall'archivio certificati:

* Trovare il certificato.
  * Cercare un certificato client rilasciato dalla stessa autorità di certificazione.
* Fare doppio clic sul certificato.
* Selezionare la scheda Percorso certificazione nella finestra di dialogo Certificato.
* Fare doppio clic sulla voce relativa alla CA inclusa nel percorso.
* Prendere nota delle proprietà del certificato.
* Chiudere la finestra di dialogo **Certificato** .
* Trovare il certificato.
  * Cercare la CA annotata in precedenza.
* Fare clic su Azioni -> Tutte le attività -> Esporta.
* Esportare il certificato in un file con estensione CER con queste opzioni:
  * **No, non esportare la chiave privata**
  * Se possibile, includi tutti i certificati nel percorso certificazione.
  * Esportare tutte le proprietà estese.

## <a name="upload-ca-certificate-to-cloud-service"></a>Caricare un certificato della CA nel servizio cloud
Caricare il certificato con il file PFX esistente o generato con la coppia di chiavi SSL.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Aggiornare il certificato della CA nel file di configurazione del servizio
Aggiornare il valore di identificazione personale della seguente impostazione nel file di configurazione del servizio con l'identificazione personale del certificato caricato nel servizio cloud:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Aggiornare il valore della seguente impostazione con la stessa identificazione personale:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Rilasciare certificati client
Ogni utente con l'autorizzazione di accesso al servizio deve avere un certificato client rilasciato per proprio uso esclusivo e scegliere una propria password complessa per proteggere la chiave privata. 

Seguire questa procedura nello stesso computer in cui è stato generato e archiviato il certificato CA autofirmato:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalizzazione

* -n con un ID per il client che verrà autenticato con il certificato.
* -e con la data di scadenza del certificato.
* MyID.pvk e MyID.cer con nomi file univoci per il certificato client

Questo comando richiederà la creazione di una password che verrà quindi usata una sola volta. Usare una password complessa.

## <a name="create-pfx-files-for-client-certificates"></a>Creare file PFX per i certificati client
Per ogni certificato client generato, eseguire:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizzazione

    MyID.pvk and MyID.cer with the filename for the client certificate

Immettere la password e quindi esportare il certificato con queste opzioni:

* Sì, esporta la chiave privata
* Esporta tutte le proprietà estese
* L'utente a cui viene rilasciato il certificato deve scegliere la password di esportazione.

## <a name="import-client-certificate"></a>Importare il certificato client
Ogni utente per il quale è stato rilasciato un certificato client dovrà importare la coppia di chiavi nei computer che userà per comunicare con il servizio:

* Fare doppio clic sul file con estensione CER in Esplora risorse.
* Importare il certificato nell'archivio personale con selezionata almeno questa opzione:
  * Includi tutte le proprietà estese.

## <a name="copy-client-certificate-thumbprints"></a>Copiare le identificazioni personali del certificato client
Ogni utente per il quale è stato rilasciato un certificato client dovrà seguire questa procedura per ottenere l'identificazione personale del proprio certificato, che verrà aggiunto al file di configurazione del servizio:

* Eseguire certmgr.exe.
* Selezionare la scheda Personale.
* Fare doppio clic sul certificato client da usare per l'autenticazione.
* Nella finestra di dialogo Certificato visualizzata selezionare la scheda Dettagli.
* Assicurarsi che in Mostra sia visualizzato Tutti.
* Nell'elenco selezionare il campo denominato Identificazione personale.
* Copiare il valore dell'identificazione personale ** Eliminare i caratteri Unicode non visibili davanti alla prima cifra ** Eliminare tutti gli spazi

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurare i client consentiti nel file di configurazione del servizio
Aggiornare il valore della seguente impostazione nel file di configurazione del servizio con un elenco delimitato da virgole delle identificazioni personali dei certificati client a cui è consentito accedere al servizio:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Configurare il controllo della revoca del certificato client
Per impostazione predefinita,lo stato della revoca del certificato non viene verificato con l'Autorità di certificazione. Per abilitare i controlli, se l'Autorità di certificazione che ha rilasciato i certificati client li supporta, modificare la seguente impostazione con uno dei valori definiti nell'enumerazione X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Creare un file PFX per certificati di crittografia autofirmati
Per un certificato di crittografia eseguire:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizzazione

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Immettere la password e quindi esportare il certificato con queste opzioni:

* Sì, esporta la chiave privata
* Esporta tutte le proprietà estese
* Quando si carica il certificato nel servizio cloud, sarà necessaria la password.

## <a name="export-encryption-certificate-from-certificate-store"></a>Esportare il certificato di crittografia dall'archivio certificati
* Trovare il certificato.
* Fare clic su Azioni -> Tutte le attività -> Esporta.
* Esportare il certificato in un file PFX con queste opzioni: 
  * Sì, esporta la chiave privata
  * Se possibile, includi tutti i certificati nel percorso certificazione 
* Esporta tutte le proprietà estese

## <a name="upload-encryption-certificate-to-cloud-service"></a>Caricare il certificato di crittografia nel servizio cloud
Caricare il certificato con il file PFX esistente o generato con la coppia di chiavi di crittografia:

* Immettere la password che protegge le informazioni sulla chiave privata.

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aggiornare il certificato di crittografia nel file di configurazione del servizio
Aggiornare il valore di identificazione personale della seguente impostazione nel file di configurazione del servizio con l'identificazione personale del certificato caricato nel servizio cloud:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Operazioni comuni relative ai certificati
* Configurare il certificato SSL.
* Configurare i certificati client.

## <a name="find-certificate"></a>Trovare il certificato.
A tale scopo, seguire questa procedura:

1. Eseguire mmc.exe.
2. File -> Aggiungi/Rimuovi snap-in.
3. Selezionare **Certificati**.
4. Fare clic su **Aggiungi**.
5. Scegliere il percorso dell'archivio certificati.
6. Fare clic su **Finish**.
7. Fare clic su **OK**.
8. Espandere **Certificati**.
9. Espandere il nodo dell'archivio certificati.
10. Espandere il nodo figlio Certificato.
11. Selezionare un certificato nell'elenco.

## <a name="export-certificate"></a>Esportare il certificato
In **Esportazione guidata certificati**:

1. Fare clic su **Avanti**.
2. Selezionare **Sì** e quindi **Esporta la chiave privata**.
3. Fare clic su **Avanti**.
4. Selezionare il formato del file di output desiderato.
5. Controllare le opzioni desiderate.
6. Selezionare **Password**.
7. Immettere una password complessa e confermarla.
8. Fare clic su **Avanti**.
9. Digitare o cercare un nome file in cui archiviare il certificato (usare un'estensione PFX).
10. Fare clic su **Avanti**.
11. Fare clic su **Finish**.
12. Fare clic su **OK**.

## <a name="import-certificate"></a>Importare il certificato
In Esportazione guidata certificati:

1. Selezionare il percorso dell'archivio.
   
   * Selezionare **Utente corrente** se solo i processi eseguiti dall'utente corrente accederanno al servizio.
   * Selezionare **Computer locale** se altri processi nel computer accederanno al servizio.
2. Fare clic su **Avanti**.
3. Se l'importazione viene effettuata da un file, verificare il percorso del file.
4. Se si importa un file PFX:
   1. Immettere la password che protegge la chiave privata.
   2. Selezionare le opzioni di importazione.
5. Selezionare "Colloca" tutti i certificati nel seguente archivio.
6. Fare clic su **Sfoglia**.
7. Selezionare l'archivio da usare.
8. Fare clic su **Finish**.
   
   * Se è stato scelto l'archivio dell'autorità di certificazione radice attendibile, fare clic su **Sì**.
9. Fare clic su **OK** in tutte le finestre di dialogo.

## <a name="upload-certificate"></a>Caricamento del certificato
Nel [portale di Azure](https://portal.azure.com/)

1. Selezionare **Servizi cloud**.
2. Selezionare il servizio cloud.
3. Nel menu superiore fare clic su **Certificati**.
4. Nella barra inferiore fare clic su **Carica**.
5. Selezionare il file del certificato.
6. Se è un file con estensione PFX, immettere la password per la chiave privata.
7. Una volta completata l'operazione, copiare l'identificazione personale del certificato dalla nuova voce nell'elenco.

## <a name="other-security-considerations"></a>Altre considerazioni sulla sicurezza
Le impostazioni SSL descritte in questo documento crittografano le comunicazioni tra il servizio e i relativi client quando si usa l'endpoint HTTPS. Questo aspetto è importante perché nella comunicazione sono contenute le credenziali per l'accesso al database e potenzialmente altre informazioni riservate. Si noti che il servizio mantiene lo stato interno, incluse le credenziali, nelle relative tabelle interne del database SQL di Microsoft Azure fornite per l'archiviazione dei metadati nella sottoscrizione di Microsoft Azure. Tale database è stato definito come parte della seguente impostazione nel file di configurazione del servizio (file CSCFG): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Le credenziali archiviate in questo database vengono crittografate. Come procedura consigliata è opportuno verificare tuttavia che i ruoli Web e di lavoro delle distribuzioni del servizio siano sempre aggiornati e protetti, in quanto dispongono dell'accesso al database di metadati e al certificato usati per la crittografia e decrittografia delle credenziali archiviate. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


