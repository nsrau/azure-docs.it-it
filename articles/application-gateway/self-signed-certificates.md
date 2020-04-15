---
title: Generare un certificato autofirmato con una CA radice personalizzata
titleSuffix: Azure Application Gateway
description: Informazioni su come generare un certificato autofirmato del gateway applicazione di Azure con una CA radice personalizzataLearn how to generate an Azure Application Gateway self-signed certificate with a custom root CA
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 5ceefb076b63df942cfff202946f6b82050bbab9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311933"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Generare un certificato autofirmato del gateway applicazione di Azure con una CA radice personalizzataGenerate an Azure Application Gateway self-signed certificate with a custom root CA

Lo SKU del gateway applicazione v2 introduce l'utilizzo di certificati radice attendibili per consentire i server back-end. In questo modo vengono rimossi i certificati di autenticazione necessari nello SKU v1. Il *certificato radice* è un X.509 con codifica Base 64(. CER) formatta il certificato radice dal server dei certificati back-end. Identifica l'autorità di certificazione (CA) radice che ha emesso il certificato server e il certificato del server viene quindi utilizzato per la comunicazione TLS/SSL.

Il gateway applicazione considera attendibile il certificato del sito Web per impostazione predefinita se è firmato da un'autorità di certificazione nota, ad esempio GoDaddy o DigiCert. In tal caso non è necessario caricare in modo esplicito il certificato radice. Per ulteriori informazioni, vedere [Panoramica della terminazione TLS e TLS end-to-end con Gateway applicazione](ssl-overview.md). Tuttavia, se si dispone di un ambiente di sviluppo/test e non si desidera acquistare un certificato firmato ca verificato, è possibile creare una CA personalizzata e creare un certificato autofirmato con esso. 

> [!NOTE]
> I certificati autofirmati non sono considerati attendibili per impostazione predefinita e possono essere difficili da gestire. Inoltre, possono utilizzare suite di hash e cifrature obsolete che potrebbero non essere complesse. Per una maggiore sicurezza, acquistare un certificato firmato da un'autorità di certificazione nota.

In questo articolo verrà spiegato come:

- Creare un'autorità di certificazione personalizzataCreate your own custom Certificate Authority
- Creare un certificato autofirmato firmato dalla CA personalizzataCreate a self-signed certificate signed by your custom CA
- Caricare un certificato radice autofirmato in un gateway applicazione per autenticare il server back-end

## <a name="prerequisites"></a>Prerequisiti

- **[OpenSSL](https://www.openssl.org/) su un computer che esegue Windows o Linux** 

   Mentre potrebbero essere disponibili altri strumenti per la gestione dei certificati, questa esercitazione usa OpenSSL.While there could be other tools available for certificate management, this tutorial uses OpenSSL. È possibile trovare OpenSSL in bundle con molte distribuzioni Linux, come Ubuntu.
- **Un server web**

   Ad esempio, Apache, IIS o NGINX per testare i certificati.

- **Uno SKU del gateway applicazione v2An Application Gateway v2 SKU**
   
  Se non si dispone di un gateway applicazione esistente, vedere Guida introduttiva: Traffico Web diretto con Il gateway applicazione di Azure - Portale di Azure.If you don't have an existing application gateway, [see Quickstart: Direct web traffic with Azure Application Gateway - Azure portal](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Creare un certificato CA radiceCreate a root CA certificate

Creare il certificato della CA radice utilizzando OpenSSL.

### <a name="create-the-root-key"></a>Creare la chiave radiceCreate the root key

1. Accedere al computer in cui è installato OpenSSL ed eseguire il comando seguente. In questo modo viene creata una chiave protetta da password.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Quando richiesto, digitare una password complessa. Ad esempio, almeno nove caratteri, utilizzando lettere maiuscole, minuscole, numeri e simboli.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Creare un certificato radice e autofirmarloCreate a Root Certificate and self-sign it

1. Utilizzare i comandi seguenti per generare il csr e il certificato.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   I comandi precedenti creano il certificato radice. Verrà utilizzato per firmare il certificato del server.

1. Quando richiesto, digitare la password per la chiave radice e le informazioni sull'organizzazione per la CA personalizzata, ad esempio paese, stato, organizzazione, unità organizzativa e il nome di dominio completo (questo è il dominio dell'autorità emittente).

   ![creare un certificato radice](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Creare un certificato serverCreate a server certificate

Successivamente, si creerà un certificato server utilizzando OpenSSL.

### <a name="create-the-certificates-key"></a>Creare la chiave del certificatoCreate the certificate's key

Utilizzare il comando seguente per generare la chiave per il certificato server.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Creare la CSR (Certificate Signing Request)

La CSR è una chiave pubblica assegnata a un'autorità di certificazione quando si richiede un certificato. La CA emette il certificato per questa richiesta specifica.

> [!NOTE]
> Il CN (nome comune) per il certificato server deve essere diverso dal dominio dell'autorità emittente. Ad esempio, in questo caso, il `www.contoso.com` CN per l'autorità `www.fabrikam.com`emittente è e CN del certificato server è .


1. Utilizzare il comando seguente per generare la richiesta CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Quando richiesto, digitare la password per la chiave radice e le informazioni sull'organizzazione per la CA personalizzata: Paese, Stato, Organizzazione, OU e il nome di dominio completo. Questo è il dominio del sito web e dovrebbe essere diverso dall'emittente.

   ![Certificato server](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Generare il certificato con il CSR e la chiave e firmarlo con la chiave radice della CA

1. Utilizzare il comando seguente per creare il certificato:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Verificare il certificato appena creato

1. Utilizzare il comando seguente per stampare l'output del file CRT e verificarne il contenuto:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Verifica del certificato](media/self-signed-certificates/verify-cert.png)

1. Verificare i file nella directory e assicurarsi di disporre dei seguenti file:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Configurare il certificato nelle impostazioni TLS del server Web

Nel server Web configurare TLS utilizzando i file fabrikam.crt e fabrikam.key. Se il server Web non accetta due file, è possibile combinarli in un singolo file con estensione pem o pfx utilizzando i comandi OpenSSL.

### <a name="iis"></a>IIS

Per istruzioni su come importare i certificati e caricarli come certificato server in IIS, vedere [HOW TO: Install Imported Certificates on a Web Server in Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Per istruzioni sull'associazione TLS, vedere [Configurazione di SSL in IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

La configurazione seguente è un esempio di host virtuale configurato per SSL in Apache:The following configuration is an example [virtual host configured for SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) in Apache:

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

La configurazione seguente è un blocco server NGINX di esempio con configurazione TLS:The following configuration is an example [NGINX server block](https://nginx.org/docs/http/configuring_https_servers.html) with TLS configuration:

![NGINX con TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Accedere al server per verificare la configurazione

1. Aggiungere il certificato radice all'archivio radice attendibile del computer. Quando si accede al sito Web, assicurarsi che l'intera catena di certificati sia visualizzata nel browser.

   ![Certificati radice trusted](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Si presuppone che DNS sia stato configurato per indirizzare il nome del server Web (in questo esempio, www.fabrikam.com) all'indirizzo IP del server Web. In caso contrario, è possibile modificare il [file hosts](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) per risolvere il nome.
1. Passare al sito Web e fare clic sull'icona del lucchetto nella casella dell'indirizzo del browser per verificare le informazioni sul sito e sul certificato.

## <a name="verify-the-configuration-with-openssl"></a>Verificare la configurazione con OpenSSL

In alternativa, è possibile utilizzare OpenSSL per verificare il certificato.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verifica certificato OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Caricare il certificato radice nelle impostazioni HTTP del gateway applicazioneUpload the root certificate to Application Gateway's HTTP Settings

Per caricare il certificato nel gateway applicazione, è necessario esportare il certificato con estensione crt in un formato con estensione cer codificato in base 64. Poiché .crt contiene già la chiave pubblica nel formato codificato in base 64, è sufficiente rinominare l'estensione del file da .crt a .cer. 

### <a name="azure-portal"></a>Portale di Azure

Per caricare il certificato radice attendibile dal portale, selezionare **Impostazioni HTTP** e scegliere il protocollo **HTTPS.**

![Aggiungere un certificato tramite il portaleAdd a certificate using the portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per caricare il certificato radice. Il codice seguente è un esempio di Azure PowerShell.The following code is an Azure PowerShell sample.

> [!NOTE]
> Nell'esempio seguente viene aggiunto un certificato radice attendibile al gateway applicazione, viene creata una nuova impostazione HTTP e viene aggiunta una nuova regola, presupponendo che il pool back-end e il listener esistano già.

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

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
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

### <a name="verify-the-application-gateway-backend-health"></a>Verificare l'integrità del back-end del gateway applicazioneVerify the application gateway backend health

1. Fare clic sulla visualizzazione **Integrità back-end** del gateway applicazione per verificare se il probe è integro.
1. Si dovrebbe vedere che lo stato è **integro** per il probe HTTPS.

![Probe HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su SSL, in Gateway applicazione, vedere [Panoramica della terminazione TLS e End-to-End TLS con Gateway applicazione](ssl-overview.md).

