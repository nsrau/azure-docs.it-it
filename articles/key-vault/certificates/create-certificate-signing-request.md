---
title: Creazione e unione di CSR in Azure Key Vault
description: Creazione e unione di CSR in Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: c8f11f17c9e110509dcbcda291194f9b8d928c50
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658962"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Creazione e unione di CSR in Key Vault

Azure Key Vault supporta l'archiviazione del certificato digitale emesso da qualsiasi autorità di certificazione nell'insieme di credenziali delle chiavi. Consente di creare la richiesta di firma del certificato con una coppia di chiavi pubblica/privata che può essere firmata dall'autorità di certificazione scelta. Può trattarsi di un'autorità di certificazione aziendale interna o di un'autorità di certificazione pubblica esterna. Una richiesta di firma del certificato (nota anche come CSR o richiesta di certificazione) è un messaggio che viene inviato dall'utente a un'autorità di certificazione per richiedere il rilascio di un certificato digitale.

Per informazioni più generali sui certificati, vedere [Certificati di Azure Key Vault](./about-certificates.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>Aggiunta in Key Vault del certificato emesso da una CA partner
Key Vault collabora con le due autorità di certificazione seguenti per semplificare la creazione di certificati. 

|Provider|Tipo di certificato|Eseguire la configurazione  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault offre certificati SSL OV o EV con DigiCert| [Guida all'integrazione](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault offre certificati SSL OV o EV con GlobalSign| [Guida all'integrazione](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>Aggiunta in Key Vault del certificato emesso da una CA non partner

La procedura seguente consente di creare un certificato rilasciato da autorità di certificazione non associate a Key Vault, come nel caso di GoDaddy, che non è un'autorità di certificazione attendibile dell'insieme di credenziali delle chiavi. 


### <a name="azure-powershell"></a>Azure PowerShell



1.  In primo luogo, **creare i criteri del certificato**. Key Vault non registrerà o rinnoverà il certificato rilasciato dall'autorità di certificazione per conto dell'utente, perché l'autorità di certificazione scelta in questo scenario non è inclusa tra quelle supportate e di conseguenza IssuerName è impostato su Unknown.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Creare una **richiesta di firma del certificato**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Recupero della **richiesta CSR firmata dall'autorità di certificazione**. `$certificateOperation.CertificateSigningRequest` è la richiesta di firma del certificato con codifica Base4 per il certificato. È possibile usare questo BLOB per eseguirne il dump nel sito Web della richiesta di certificato dell'autorità di certificazione. Questo passaggio varia a seconda dell'autorità di certificazione. Il modo migliore consiste nell'esaminare le linee guida dell'autorità di certificazione per informazioni su come eseguire questo passaggio. È anche possibile usare strumenti come certreq o openssl per ottenere la richiesta di certificato firmata e completare il processo di generazione di un certificato.


4. **Unione della richiesta firmata** in Key Vault. Dopo che la richiesta di certificato è stata firmata dall'autorità di certificazione, è possibile ripristinare il certificato firmato e unirlo con la coppia di chiavi pubblica/privata iniziale creata in Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    La richiesta di certificato è stata ora unita correttamente.

### <a name="azure-portal"></a>Portale di Azure

1.  Per generare la richiesta CSR per l'autorità di certificazione scelta, passare all'insieme di credenziali delle chiavi in cui aggiungere il certificato.
2.  Nella pagina delle proprietà di Key Vault selezionare **Certificati**.
3.  Selezionare la scheda **Genera/Importa**.
4.  Nella schermata **Crea un certificato** scegliere i valori seguenti:
    - **Metodo di creazione del certificato:** Genera.
    - **Nome del certificato:** ContosoManualCSRCertificate.
    - **Tipo di Autorità di certificazione:** Certificato rilasciato da un'autorità di certificazione non integrata
    - **Soggetto:** `"CN=www.contosoHRApp.com"`
    - Selezionare gli altri valori desiderati. Fare clic su **Crea**.

    ![Proprietà del certificato](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Si noterà che il certificato è stato aggiunto all'elenco dei certificati. Selezionare il nuovo certificato appena creato. Lo stato corrente del certificato sarà 'disabilitato' perché non è ancora stato rilasciato dall'autorità di certificazione.
7. Fare clic sulla scheda **Operazione relativa al certificato** e selezionare **Scarica file CSR**.
 ![Screenshot con il pulsante Scarica file CSR evidenziato.](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Sottoporre il file con estensione csr all'autorità di certificazione in modo che la richiesta venga firmata.
9.  Dopo che la richiesta è stata firmata dall'autorità di certificazione, ripristinare il file del certificato per **unire la richiesta firmata** nella stessa schermata dell'operazione relativa al certificato.

La richiesta di certificato è stata ora unita correttamente.

## <a name="adding-more-information-to-csr"></a>Aggiunta di altre informazioni a CSR

Se si vogliono aggiungere altre informazioni durante la creazione di CSR, ad esempio: 
    - Paese:
    - Città/Località:
    - Regione/Provincia:
    - Organizzazione:
    - Unità organizzativa: è possibile aggiungere tutte queste informazioni quando si crea una CSR definendole in SubjectName.

Esempio
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

>[!Note]
>Se si richiede un certificato DV con tutti questi dettagli in CSR, è possibile che l'autorità di certificazione rifiuti la richiesta perché potrebbe non essere in grado di convalidare tutte le informazioni contenute nella richiesta. Se si richiede un certificato OV, sarebbe più opportuno aggiungere tutte queste informazioni in CSR.


## <a name="troubleshoot"></a>Risolvere problemi

- **Il tipo di errore 'La chiave pubblica del certificato di entità finale nel contenuto del certificato X.509 specificato non corrisponde alla parte pubblica della chiave privata specificata. Verificare se il certificato sia valido'** Questo errore può verificarsi se non si unisce la CSR con la stessa richiesta CSR avviata. Ogni volta che si crea una CSR, si crea una chiave privata che deve essere confrontata durante l'unione della richiesta firmata.
    
- In caso di merge della CSR viene eseguito il merge dell'intera catena?
    Sì, verrà eseguito il merge dell'intera catena, purché l'utente abbia ripristinato il file p7b di cui eseguire il merge.

- Se il certificato rilasciato si trova nello stato 'disabilitato' nel portale di Azure, vedere **Operazione relativa al certificato** per esaminare il messaggio di errore per il certificato.

Per altre informazioni, vedere le [operazioni relative ai certificati nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy).

## <a name="next-steps"></a>Passaggi successivi

- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
