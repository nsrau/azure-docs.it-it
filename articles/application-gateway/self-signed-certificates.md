---
title: Generare un certificato autofirmato applicazione Azure gateway con una CA radice personalizzata
description: Informazioni su come generare un certificato autofirmato applicazione Azure gateway con una CA radice personalizzata
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 84a46e66bb6c36950a84fbeb2dacc3a8d6bcc241
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833364"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Generare un certificato autofirmato applicazione Azure gateway con una CA radice personalizzata

Lo SKU del gateway applicazione V2 introduce l'uso di certificati radice attendibili per consentire i server back-end. In questo modo vengono rimossi i certificati di autenticazione necessari nello SKU V1. Il *certificato radice* è un X. 509 con codifica base 64 (. CER) formattare il certificato radice dal server di certificati back-end. Identifica l'autorità di certificazione radice (CA) che ha emesso il certificato del server e il certificato del server viene quindi utilizzato per la comunicazione SSL.

Il gateway applicazione considera attendibile il certificato del sito Web per impostazione predefinita se è firmato da un'autorità di certificazione nota (ad esempio, GoDaddy o DigiCert). In tal caso, non è necessario caricare in modo esplicito il certificato radice. Per altre informazioni, vedere [Panoramica della terminazione SSL e del protocollo SSL end-to-end con il gateway applicazione](ssl-overview.md). Tuttavia, se si dispone di un ambiente di sviluppo/test e non si desidera acquistare un certificato firmato da un'autorità di certificazione, è possibile creare una CA personalizzata e creare un certificato autofirmato. 

> [!NOTE]
> I certificati autofirmati non sono attendibili per impostazione predefinita e possono essere difficili da gestire. Inoltre, possono utilizzare pacchetti di crittografia e hash obsoleti che potrebbero non essere sicuri. Per una maggiore sicurezza, acquistare un certificato firmato da un'autorità di certificazione nota.

In questo articolo verrà spiegato come:

- Creare un'autorità di certificazione personalizzata
- Creare un certificato autofirmato firmato dalla CA personalizzata
- Caricare un certificato radice autofirmato in un gateway applicazione per autenticare il server back-end

## <a name="prerequisites"></a>Prerequisiti

- **[Openssl](https://www.openssl.org/) in un computer che esegue Windows o Linux** 

   Sebbene siano disponibili altri strumenti per la gestione dei certificati, in questa esercitazione viene usato OpenSSL. È possibile trovare OpenSSL in bundle con molte distribuzioni di Linux, ad esempio Ubuntu.
- **Un server Web**

   Ad esempio, Apache, IIS o NGINX per testare i certificati.

- **SKU del gateway applicazione V2**
   
  Se non si dispone di un gateway applicazione esistente, vedere [Guida introduttiva: indirizzare il traffico Web con applicazione Azure portale di Azure gateway](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Creare un certificato CA radice

Creare il certificato CA radice usando OpenSSL.

### <a name="create-the-root-key"></a>Creare la chiave radice

1. Accedere al computer in cui è installato OpenSSL ed eseguire il comando seguente. Viene creata una chiave protetta da password.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Al prompt dei comandi digitare una password complessa. Ad esempio, almeno nove caratteri, usando lettere maiuscole, lettere minuscole, numeri e simboli.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Creare un certificato radice e firmarlo autonomamente

1. Usare i comandi seguenti per generare il certificato CSR e il certificato.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   I comandi precedenti creano il certificato radice. Che verrà usato per firmare il certificato del server.

1. Quando richiesto, digitare la password per la chiave radice e le informazioni sull'organizzazione per l'autorità di certificazione personalizzata, ad esempio paese, stato, organizzazione, OU e il nome di dominio completo (si tratta del dominio dell'emittente).

   ![Crea certificato radice](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Creazione di un certificato server

Successivamente, verrà creato un certificato server usando OpenSSL.

### <a name="create-the-certificates-key"></a>Creare la chiave del certificato

Usare il comando seguente per generare la chiave per il certificato del server.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Creare la richiesta di firma del certificato (CSR)

CSR è una chiave pubblica assegnata a una CA quando viene richiesto un certificato. La CA rilascia il certificato per questa richiesta specifica.

> [!NOTE]
> Il CN (nome comune) per il certificato server deve essere diverso dal dominio dell'emittente. In questo caso, ad esempio, il CN per l'emittente è `www.contoso.com` e il CN del certificato server è `www.fabrikam.com`.


1. Usare il comando seguente per generare il CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Quando richiesto, digitare la password per la chiave radice e le informazioni sull'organizzazione per la CA personalizzata: paese, stato, organizzazione, unità organizzativa e nome di dominio completo. Questo è il dominio del sito Web e deve essere diverso dall'emittente.

   ![Certificato server](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Generare il certificato con CSR e la chiave e firmarla con la chiave radice della CA

1. Usare il comando seguente per creare il certificato:

   ```
   openssl x509 -req -in fabrikam.csr -CA public.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Verificare il certificato appena creato

1. Usare il comando seguente per stampare l'output del file CRT e verificarne il contenuto:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Verifica del certificato](media/self-signed-certificates/verify-cert.png)

1. Verificare i file nella directory e assicurarsi che siano presenti i file seguenti:

   - contoso. CRT
   - contoso. Key
   - Fabrikam. CRT
   - Fabrikam. Key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Configurare il certificato nelle impostazioni SSL del server Web

Nel server Web configurare SSL usando i file Fabrikam. CRT e fabrikam. Key. Se il server Web non può eseguire due file, è possibile combinarli in un singolo file con estensione PEM o PFX usando i comandi OpenSSL.

### <a name="iis"></a>IIS

Per istruzioni su come importare e caricare il certificato come certificato server in IIS, vedere [procedura: installare i certificati importati in un server Web in Windows server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Per istruzioni sull'associazione SSL, vedere [come configurare SSL in IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

La configurazione seguente è un [host virtuale di esempio configurato per SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) in Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

La configurazione seguente è un esempio di [blocco del server nginx](https://nginx.org/docs/http/configuring_https_servers.html) con configurazione SSL:

![NGINX con SSL](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Accedere al server per verificare la configurazione

1. Aggiungere il certificato radice all'archivio radice attendibile del computer. Quando si accede al sito Web, assicurarsi che l'intera catena di certificati venga visualizzata nel browser.

   ![Certificati radice trusted](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Si presuppone che il DNS sia stato configurato in modo da puntare il nome del server Web (in questo esempio, www.fabrikam.com) all'indirizzo IP del server Web. In caso contrario, è possibile modificare il [file hosts](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) per risolvere il nome.
1. Passare al sito Web e fare clic sull'icona a forma di lucchetto nella casella dell'indirizzo del browser per verificare il sito e le informazioni sul certificato.

## <a name="verify-the-configuration-with-openssl"></a>Verificare la configurazione con OpenSSL

In alternativa, è possibile usare OpenSSL per verificare il certificato.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verifica del certificato OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Caricare il certificato radice nelle impostazioni HTTP del gateway applicazione

Per caricare il certificato nel gateway applicazione, è necessario esportare il certificato. CRT in un formato con estensione cer con codifica base-64. Poiché. CRT contiene già la chiave pubblica nel formato con codifica base 64, è sufficiente rinominare l'estensione di file da. CRT a. cer. 

### <a name="azure-portal"></a>Portale di Azure

Per caricare il certificato radice attendibile dal portale, selezionare le **impostazioni http** e scegliere il protocollo **https** .

![Aggiungere un certificato tramite il portale](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per caricare il certificato radice. Il codice seguente è un esempio di Azure PowerShell.

> [!NOTE]
> Nell'esempio seguente viene aggiunto un certificato radice attendibile al gateway applicazione, viene creata una nuova impostazione HTTP e viene aggiunta una nuova regola, supponendo che il pool back-end e il listener esistano già.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don’t forget to set the “hostname” field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server’s certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```
### <a name="verify-the-application-gateway-backend-health"></a>Verificare l'integrità back-end del gateway applicazione

1. Fare clic sulla visualizzazione **integrità back-end** del gateway applicazione per verificare se il probe è integro.
1.  Si noterà che lo stato è **integro** per il probe HTTPS.

    ![Probe HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su SSL\TLS nel gateway applicazione, vedere [Cenni preliminari sulla terminazione SSL e SSL end-to-end con il gateway applicazione](ssl-overview.md).

