---
title: Risolvere i problemi di connessione da punto a sito di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi di connessione da punto a sito.
services: vpn-gateway
documentationcenter: na
author: genlin
manager: willchen
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c9d9b099bf71c5b1e51a11e3b62779bbc8767fd6
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Risoluzione dei problemi: problemi di connessione da punto a sito di Azure

Questo articolo elenca i potenziali problemi di connessione da punto a sito e le possibili cause e soluzioni.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Errore del client VPN: Impossibile trovare un certificato

### <a name="symptom"></a>Sintomo

Quando si cerca di connettersi alla rete virtuale di Microsoft Azure usando il client VPN, viene visualizzato il messaggio di errore seguente:

**Impossibile trovare un certificato utilizzabile con il protocollo EAP (Extensible Authentication Protocol). (Errore 798)**

### <a name="cause"></a>Causa

Questo problema si verifica se il certificato client non è presente in **Certificati - Utente corrente\Personale\Certificati**.

### <a name="solution"></a>Soluzione

Verificare che il certificato client sia installato nel percorso seguente dell'archivio certificati (Certmgr.msc):
 
**Certificati - Utente corrente\Personale\Certificati**

Per altre informazioni su come installare il certificato client, vedere [Generare ed esportare i certificati per le connessioni da punto a sito](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Quando si importa il certificato client, non selezionare l'opzione **Abilita protezione avanzata chiave privata**.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Errore del client VPN: Il messaggio ricevuto era imprevisto o con formattazione scorretta

### <a name="symptom"></a>Sintomo

Quando si cerca di connettersi alla rete virtuale usando il client VPN, viene visualizzato il messaggio di errore seguente:

**Errore del client VPN: Il messaggio ricevuto era imprevisto o con formattazione scorretta. (Errore 0x80090326)**

### <a name="cause"></a>Causa

Questo problema si verifica se la chiave pubblica del certificato radice non viene caricata nel gateway VPN di Microsoft Azure oppure se la chiave è danneggiata o scaduta.

### <a name="solution"></a>Soluzione

Per risolvere questo problema, controllare lo stato del certificato radice nel portale di Azure per verificare se è stato revocato. Se non è stato revocato, provare a eliminare il certificato radice e a ricaricarlo. Per altre informazioni, vedere [Creare certificati](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Errore del client VPN: Una catena di certificati è stata elaborata correttamente, ma termina 

### <a name="symptom"></a>Sintomo 

Quando si cerca di connettersi alla rete virtuale di Azure usando il client VPN, viene visualizzato il messaggio di errore seguente:

**Una catena di certificati è stata elaborata correttamente, ma termina con un certificato radice considerato non attendibile dal provider di attendibilità**

### <a name="solution"></a>Soluzione

1. Verificare che i certificati seguenti siano nel percorso corretto:

    | Certificate | Località |
    | ------------- | ------------- |
    | AzureClient.pfx  | Utente corrente\Personale\Certificati |
    | Azuregateway-*GUID*.cloudapp.net  | Utente corrente\Autorità di certificazione radice attendibili|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Computer locale\Autorità di certificazione radice attendibili|

2. Se i certificati sono già presenti nel percorso, provare a eliminarli e reinstallarli. Il certificato **azuregateway-*GUID*.cloudapp.net** si trova nel pacchetto di configurazione del client VPN scaricato dal portale di Azure. Per estrarre i file dal pacchetto, è possibile usare un archiver di file.

##  <a name="error-file-download-error-target-uri-is-not-specified"></a>Errore: "Errore di download del file L'URI di destinazione non è specificato"

### <a name="symptom"></a>Sintomo

Viene visualizzato il messaggio di errore seguente:

**Errore di download del file L'URI di destinazione non è specificato**

### <a name="cause"></a>Causa 

Questo problema si verifica a causa di un tipo di gateway non corretto. 

### <a name="solution"></a>Soluzione

Il tipo di gateway VPN deve essere **VPN** e il tipo di VPN deve essere **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed-8007026f"></a>Errore del client VPN: Impossibile eseguire lo script personalizzato per la VPN di Azure (8007026f)

### <a name="symptom"></a>Sintomo

Quando si cerca di connettersi alla rete virtuale di Azure usando il client VPN, viene visualizzato il messaggio di errore seguente:

**Impossibile eseguire lo script personalizzato (per aggiornare la tabella di routing) (8007026f).**

### <a name="cause"></a>Causa

Questo problema può verificarsi se si prova ad aprire la connessione VPN da sito a punto usando un collegamento.

### <a name="solution"></a>Soluzione 

Aprire direttamente il pacchetto VPN invece di aprirlo dal collegamento.

## <a name="cannot-install-the-vpn-client"></a>Non è possibile installare il client VPN

### <a name="cause"></a>Causa 

È necessario un certificato aggiuntivo per considerare attendibile il gateway VPN per la rete virtuale. Il certificato è incluso nel pacchetto di configurazione del client VPN generato dal portale di Azure.

### <a name="solution"></a>Soluzione

Estrarre il pacchetto di configurazione del client VPN. Sarà presente un file CER. Installare il certificato nelle **Autorità di certificazione radice disponibili nell'elenco locale** dell'**account computer**:

1. Aprire mmc.exe
2. Aggiungere lo snap-in **Certificati**.
3. Selezionare l'account **Computer** per il computer locale
4. Fare clic con il pulsante destro del mouse sul nodo **Autorità di certificazione radice attendibili** > **All-Task** > **Importa** e passare al file CER estratto dal pacchetto di configurazione del client VPN.
5. Riavviare il computer. 
6. Provare a installare il client VPN.

## <a name="azure-portal-error-failed-to-save-vpn-gateway-data-is-invalid"></a>Errore del portale di Azure: Non è stato possibile salvare il gateway VPN - I dati non sono validi

### <a name="symptom"></a>Sintomo

Quando si prova a salvare le modifiche per il gateway VPN nel portale di Azure, viene visualizzato il messaggio di errore seguente:

**Non è stato possibile salvare il gateway di rete virtuale &lt;nome gateway&gt;. Errore: I dati per il certificato &lt;ID certificato&gt; non sono validi.**

### <a name="cause"></a>Causa 

Questo problema può verificarsi se la chiave pubblica del certificato radice caricata contiene caratteri non validi, ad esempio uno spazio.

### <a name="solution"></a>Soluzione

Verificare che i dati nel certificato non contengano caratteri non validi, ad esempio interruzioni di riga (ritorni a capo). L'intero valore deve essere su un'unica riga lunga. Il testo seguente è un esempio del certificato:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-vpn-gateway-resource-name-is-invalid"></a>Errore del portale di Azure: Non è stato possibile salvare il gateway VPN - Nome risorsa non valido

### <a name="symptom"></a>Sintomo

Quando si prova a salvare le modifiche per il gateway VPN nel portale di Azure, viene visualizzato il messaggio di errore seguente: 

**Non è stato possibile salvare il gateway di rete virtuale &lt;nome gateway&gt;. Errore: Nome risorsa &lt;nome certificato da caricare&gt; non valido**.

### <a name="cause"></a>Causa

Questo problema si verifica perché il nome del certificato contiene caratteri non validi, ad esempio uno spazio. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Errore del portale di Azure: errore 503 relativo al download del file del pacchetto VPN

### <a name="symptom"></a>Sintomo

Quando si prova a scaricare il pacchetto di configurazione del client VPN, viene visualizzato il messaggio di errore seguente:

**Non è stato possibile scaricare il file. Dettagli errore: errore 503. Il server è occupato.**
 
### <a name="solution"></a>Soluzione

Questo errore può essere causato da un problema di rete temporaneo. Provare di nuovo a scaricare il pacchetto VPN dopo alcuni minuti.

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Aggiornamento del gateway VPN di Azure, tutti i client P2S non riescono a connettersi

### <a name="cause"></a>Causa

Se il certificato ha superato il 50% del ciclo di vita, ne viene eseguito il rollover.

### <a name="solution"></a>Soluzione

Per risolvere questo problema, creare e ridistribuire i nuovi certificati nei client VPN. 

## <a name="too-many-vpn-clients-connected-at-once"></a>Troppi client VPN connessi contemporaneamente

Per ogni gateway VPN, il numero massimo di connessioni consentite è 128.  È possibile visualizzare il numero totale di client connessi nel portale di Azure.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-route-table"></a>La VPN da punto a sito aggiunge erroneamente una route per 10.0.0.0/8 alla tabella delle route

### <a name="symptom"></a>Sintomo

Quando si stabilisce la connessione VPN sul client da punto a sito, è previsto che il client VPN aggiunga una route verso la rete virtuale di Azure e che il servizio Iphelper aggiunga una route per la subnet dei client VPN. 

Se tuttavia l'intervallo di client VPN appartiene a una subnet inferiore a 10.0.0.0/8, ad esempio 10.0.12.0/24, invece di una route per 10.0.12.0/24, viene aggiunta una route "errata" per 10.0.0.0/8, che ha priorità più elevata. 

Questo interrompe la connettività con altre reti locali che potrebbero appartenere a un'altra subnet compresa nell'intervallo 10.0.0.0/8, ad esempio 10.50.0.0/24, per cui non è definita una route specifica. 

### <a name="cause"></a>Causa

Questo è il comportamento da progettazione per i client Windows. Il client, quando usa il protocollo PPP IPCP, ottiene l'indirizzo IP per l'interfaccia del tunnel dal server, in questo caso il gateway VPN. A causa di una limitazione nel protocollo tuttavia il client non ha la subnet mask. Poiché non è possibile ottenerla in altro modo, il client prova a ipotizzare la subnet mask in base alla classe dell'indirizzo IP dell'interfaccia del tunnel. 

Viene quindi aggiunta una route in base al mapping statico seguente: 

Se l'indirizzo appartiene alla classe A --> si applica /8

Se l'indirizzo appartiene alla classe B --> si applica

Se l'indirizzo appartiene alla classe C --> si applica /24

##  <a name="vpn-client-cannot-access-network-file-shares"></a>Il client VPN non riesce ad accedere alle condivisioni file di rete

### <a name="symptom"></a>Sintomo

Il client VPN si è connesso alla rete di Azure, ma non riesce ad accedere alle condivisioni di rete.

### <a name="cause"></a>Causa

Per l'accesso alle condivisioni file, viene usato il protocollo SMB. L'errore si verifica quando le credenziali della sessione vengono aggiunte dal client VPN quando viene avviata la connessione. Dopo che la connessione è stata stabilita, il client è forzato a usare le credenziali della cache per l'autenticazione Kerberos. Vengono avviate query al Centro distribuzione chiavi (un controller di dominio) per ottenere un token. Poiché i client si connettono da Internet, potrebbero non riuscire a raggiungere il controller di dominio. I client non riescono quindi a effettuare il failover da Kerberos a NTLM. 

Le credenziali verranno richieste solo quando il client ha un certificato valido (con SAN=UPN) rilasciato dal dominio a cui è unito ed è fisicamente connesso alla rete di dominio. In questo caso, il client prova a usare il certificato e a raggiungere il controller di dominio. Il Centro distribuzione chiavi restituisce quindi un errore "KDC_ERR_C_PRINCIPAL_UNKNOWN"  che forza il client a effettuare il failover a NTLM. 

### <a name="solution"></a>Soluzione

Per risolvere il problema, disabilitare la memorizzazione nella cache delle credenziali di dominio dalla sottochiave del registro di sistema seguente: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-vpn-client"></a>Non è possibile trovare la connessione VPN da punto a sito in Windows dopo la reinstallazione del client VPN

### <a name="symptom"></a>Sintomo

Si rimuove la connessione VPN da punto a sito e quindi si reinstalla il client VPN. In questo caso, la connessione VPN non viene configurata correttamente. La connessione VPN non viene visualizzata nelle impostazioni di **Connessioni di rete** in Windows.

### <a name="solution"></a>Soluzione

Per risolvere il problema, eliminare i file di configurazione del client VPN precedenti da **C:\Utenti\NomeUtente\AppData\Roaming\Microsoft\Network\Connections** e quindi eseguire di nuovo il programma di installazione del client VPN.
