---
title: Asset di tipo certificato in Automazione di Azure | Documentazione Microsoft
description: I certificati possono essere archiviati in modo sicuro in Automazione di Azure, in modo che i Runbook o le configurazioni DSC possano accedervi per eseguire l&quot;autenticazione rispetto a risorse di Azure e di terze parti.  Questo articolo illustra nel dettaglio i certificati e spiega come usarli nella creazione testuale e grafica.
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1973a3523e121414dfbebf4d00cd2d4fe2005d2f


---
# <a name="certificate-assets-in-azure-automation"></a>Asset di tipo certificato in Automazione di Azure
I certificati possono essere archiviati in modo sicuro in Automazione di Azure, in modo che i Runbook possano accedervi tramite l'attività **Get-AutomationCertificate** . Ciò permette di creare Runbook e configurazioni DSC che usano certificati per l'autenticazione o di aggiungerli a risorse di Azure o di terze parti.

> [!NOTE]
> Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure tramite una chiave univoca generata per ogni account di automazione. La chiave viene crittografata da un certificato master e archiviata in Automazione di Azure. Prima dell'archiviazione di un asset sicuro, la chiave per l'account di automazione viene decrittografata usando il certificato master e viene quindi usata per crittografare l'asset.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlet di Windows PowerShell
I cmdlet della tabella seguente vengono usati per creare e gestire asset di certificati di automazione con Windows PowerShell. Sono inclusi nel [modulo Azure PowerShell](../powershell-install-configure.md) , disponibile per l'uso nei runbook di Automazione e nelle configurazioni DSC.

| Cmdlet | Descrizione |
|:--- |:--- |
| [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) |Recupera informazioni su un certificato. È possibile recuperare solo il certificato stesso dall'attività Get-AutomationCertificate. |
| [New- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx) |Importa un nuovo certificato in Automazione di Azure. |
| [Remove- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx) |Rimuove un certificato da Automazione di Azure. |
| [Set- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx) |Imposta le proprietà per un certificato esistente, inclusi il caricamento del file del certificato e l'impostazione della password per un file con estensione pfx. |

## <a name="activities-to-access-certificates"></a>Attività di accesso ai certificati
Le attività incluse nella tabella seguente vengono usate per accedere ai certificati in un Runbook o configurazione DSC.

| Attività | Descrizione |
|:--- |:--- |
| Get-AutomationCertificate |Ottiene un certificato da usare in un runbook o in una configurazione DSC. |

> [!NOTE]
> È consigliabile evitare di usare le variabili nel parametro –Name di Get-AutomationCertificate, perché ciò può complicare l'individuazione delle dipendenze tra runbook o configurazioni DSC e gli asset di certificato in fase di progettazione.
> 
> 

## <a name="creating-a-new-certificate"></a>Creazione di un nuovo certificato
Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in Automazione di Azure. Se si contrassegna il certificato come esportabile, sarà possibile trasferirlo all'esterno dell'archivio di certificati di Automazione di Azure. Se non è esportabile, sarà possibile usarlo solo per la firma entro il Runbook o configurazione DSC.

### <a name="to-create-a-new-certificate-with-the-azure-classic-portal"></a>Per creare un nuovo certificato con il portale di Azure classico
1. Dall'account di automazione fare clic su **Asset** nella parte superiore della finestra.
2. Nella parte inferiore della finestra, fare clic su **Aggiungi impostazione**.
3. Fare clic su **Aggiungi credenziali**.
4. Nell'elenco a discesa **Tipo di credenziali** selezionare **Certificato**.
5. Digitare un nome per il certificato nella casella **Nome** e fare clic sulla freccia a destra.
6. Cercare un file con estensione cer o pfx.  Se si seleziona un file con estensione pfx, specificare una password e indicare se ne deve essere consentita l'esportazione.
7. Fare clic sul segno di spunta per caricare il file del certificato e salvare il nuovo asset di certificato.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Per creare un nuovo certificato con il portale di Azure
1. Dall'account di automazione fare clic sulla parte **Asset** per aprire il pannello **Asset**.
2. Fare clic sulla parte **Certificati** per aprire il pannello **Certificati**.
3. Fare clic su **Aggiungi certificato** nella parte superiore del pannello.
4. Digitare un nome per il certificato nella casella **Nome** .
5. Fare clic su **Selezionare un file** in **Carica un file di certificato** per cercare un file con estensione cer o pfx.  Se si seleziona un file con estensione pfx, specificare una password e indicare se ne deve essere consentita l'esportazione.
6. Fare clic su **Crea** per salvare il nuovo asset di certificato.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Per creare un nuovo certificato con Windows PowerShell
I comandi di esempio seguenti mostrano come creare un nuovo certificato di automazione e come contrassegnarlo come esportabile. Verrà importato un file con estensione pfx esistente.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force

    New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## <a name="using-a-certificate"></a>Utilizzo di un certificato
Per usare un certificato è necessario usare l'attività **Get-AutomationCertificate** . Non è possibile usare il cmdlet [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) , poiché restituisce informazioni sull'asset di certificato, ma non il certificato stesso.

### <a name="textual-runbook-sample"></a>Esempio di Runbook testuale
Il codice di esempio seguente mostra come aggiungere un certificato a un servizio cloud in un Runbook. In questo esempio la password viene recuperata da una variabile di automazione crittografata.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AutomationVariable –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Esempio di Runbook grafico
Per aggiungere un'attività **Get-AutomationCertificate** a un runbook grafico, fare clic con il pulsante destro del mouse sul certificato nel riquadro della libreria dell'editor grafico e scegliere **Aggiungi ad area di disegno**.

![](media/automation-certificates/certificate-add-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un Runbook grafico.  Si tratta dello stesso esempio mostrato in precedenza per l'aggiunta di un certificato a un servizio cloud da un Runbook testuale.  

Questo esempio usa il set di parametri **UseConnectionObject** per l'attività **Send-TwilioSMS** che usa un oggetto connessione per l'autenticazione al servizio.  È necessario usare un [collegamento pipeline](automation-graphical-authoring-intro.md#links-and-workflow) , perché un collegamento sequenza restituirebbe una raccolta contenente un singolo oggetto, non previsto dal parametro Connection.

![](media/automation-certificates/add-certificate.png)

## <a name="see-also"></a>Vedere anche
* [Collegamenti nella creazione grafica](automation-graphical-authoring-intro.md#links-and-workflow) 




<!--HONumber=Nov16_HO3-->


