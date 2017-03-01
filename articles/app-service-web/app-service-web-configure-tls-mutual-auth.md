---
title: Come configurare l&quot;autenticazione reciproca TLS per un&quot;app Web
description: Informazioni su come configurare un&quot;app Web per l&quot;uso dell&quot;autenticazione del certificato client in TLS.
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: db2f48b248e2232f913a99b4ffbc0d18b77407e8
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Come configurare l'autenticazione reciproca TLS per un'app Web
## <a name="overview"></a>Overview
È possibile limitare l'accesso all'app web di Azure abilitandone diversi tipi di autenticazione. A questo scopo è possibile eseguire l'autenticazione usando un certificato client quando la richiesta è per TLS/SSL. Questo meccanismo è detto l'autenticazione reciproca TLS o autenticazione del certificato client. Questo articolo illustra come configurare un'app Web per l'uso dell'autenticazione del certificato client.

> **Nota:** se si accede al sito tramite HTTP e non HTTPS, non si riceveranno i certificati client. Pertanto, se l'applicazione richiede i certificati client è consigliabile non consentire le richieste all'applicazione tramite HTTP.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Configurare l'app Web per l'autenticazione del certificato client
Per configurare l'app Web per richiedere certificati client, è necessario aggiungere l'impostazione del sito clientCertEnabled per l'app Web e impostarla su true. Questa impostazione non è attualmente disponibile tramite l'esperienza di gestione nel portale e si dovrà usare l'API REST a questo scopo.

Per semplificare la definizione della chiamata all'API REST, è possibile usare lo [strumento ARMClient](https://github.com/projectkudu/ARMClient) . Dopo l'accesso con lo strumento, sarà necessario eseguire il comando seguente:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

sostituire tutti gli elementi tra {} con le informazioni per l'app Web e la creare un file denominato enableclientcert.json con il contenuto JSON seguente:

> { "location": "My Web App Location",   
> "properties": {  
> "clientCertEnabled": true } }  
> 
> 

Assicurarsi di modificare il valore di "posizione" con la posizione in cui si trova l'app Web, ad esempio Stati Uniti centro-settentrionali o Stati Uniti occidentali e così via.

> **Nota:** se si esegue ARMClient da Powershell, è necessario eseguire l'escape del simbolo @ per il file JSON con un apice inverso `.
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Accesso al certificato client dall'app Web
Se si usa ASP.NET e si configura l'applicazione per l'uso dell'autenticazione del certificato client, il certificato sarà disponibile tramite la proprietà **HttpRequest.ClientCertificate** . Per gli altri stack di applicazioni, il certificato client è disponibile nell'app tramite un valore con codifica Base64 nell'intestazione della richiesta "X-ARR ClientCert". L'applicazione può creare un certificato da questo valore e quindi usarlo a scopo di autenticazione e autorizzazione nell'applicazione.

## <a name="special-considerations-for-certificate-validation"></a>Considerazioni speciali per la convalida del certificato
Il certificato client inviato all'applicazione non viene sottoposto ad alcuna convalida da parte della piattaforma di app Web di Azure. La convalida il certificato è responsabilità dell'app Web. Ecco un esempio di codice ASP.NET che convalida le proprietà del certificato a scopo di autenticazione.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }

