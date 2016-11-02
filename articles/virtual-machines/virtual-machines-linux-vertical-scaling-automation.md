<properties
	pageTitle="Scalabilità verticale di macchine virtuali di Azure tramite Automazione di Azure | Microsoft Azure"
	description="Come eseguire la scalabilità verticale di una macchina virtuale Linux in risposta agli avvisi di monitoraggio tramite Automazione di Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="singhkay"/>

# Scalabilità verticale di macchine virtuali di Azure tramite Automazione di Azure

La scalabilità verticale è il processo di aumento o riduzione delle risorse di una macchina in risposta al carico di lavoro. In Azure tale operazione può essere eseguita modificando le dimensioni della macchina virtuale. Può essere utile negli scenari seguenti:

- Se la macchina virtuale non viene usata di frequente, è possibile diminuirne le dimensioni per ridurre i costi mensili
- Se nella macchina virtuale si osserva un picco di carico, è possibile aumentarne le dimensioni per una maggiore capacità

Per eseguire questa operazione, seguire questa procedura:

1. Configurare Automazione di Azure per l'accesso alle macchine virtuali
2. Importare i runbook di scalabilità verticale di Automazione di Azure nella sottoscrizione
3. Aggiungere un webhook al runbook
4. Aggiungere un avviso alla macchina virtuale

> [AZURE.NOTE] A causa delle dimensioni della prima macchina virtuale, le dimensioni a cui la macchina può essere ridimensionata possono essere limitate a seconda della disponibilità di altre dimensioni nel cluster in cui viene distribuita la macchina virtuale corrente. Nei runbook di automazione pubblicati usati in questo articolo viene considerato questo caso e la scalabilità viene applicata solo all'interno delle coppie di dimensioni delle macchine virtuali seguenti. Pertanto, una macchina virtuale Standard\_D1v2 non verrà improvvisamente ridimensionata verso l'alto a una Standard\_G5 o verso il basso a una Basic\_A0.

>| coppie di ridimensionamento di dimensioni delle macchine virtuali | |
|---|---|
| Basic\_A0 | Basic\_A4 |
| Standard\_A0 | Standard\_A4 |
| Standard\_A5 | Standard\_A7 |
| Standard\_A8 | Standard\_A9 |
| Standard\_A10 | Standard\_A11 |
| Standard\_D1 | Standard\_D4 |
| Standard\_D11 | Standard\_D14 |
| Standard\_DS1 | Standard\_DS4 |
| Standard\_DS11 | Standard\_DS14 |
| Standard\_D1v2 | Standard\_D5v2 |
| Standard\_D11v2 | Standard\_D14v2 |
| Standard\_G1 | Standard\_G5 |
| Standard\_GS1 | Standard\_GS5 |

## Configurare Automazione di Azure per l'accesso alle macchine virtuali

La prima operazione da eseguire è creare l'account di Automazione di Azure che ospiterà i runbook usati per ridimensionare le istanze del set di scalabilità VM. Il servizio Automazione ha introdotto di recente la funzionalità "Account RunAs", che semplifica molto l'impostazione dell'entità servizio per l'esecuzione automatica di runbook per conto dell'utente. Altre informazioni sono disponibili nell'articolo seguente.

* [Autenticare runbook con account RunAs di Azure](../automation/automation-sec-configure-azure-runas-account.md)

## Importare i runbook di scalabilità verticale di Automazione di Azure nella sottoscrizione

I runbook necessari per la scalabilità verticale della macchina virtuale sono già stati pubblicati nella raccolta dei runbook di Automazione di Azure. Sarà necessario importarli nella sottoscrizione. Per informazioni sull'importazione dei runbook, vedere l'articolo seguente:

* [Raccolte di runbook e moduli per l'automazione di Azure](../automation/automation-runbook-gallery.md)

I runbook da importare sono visualizzati nell'immagine seguente:

![Importazione runbook](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## Aggiungere un webhook al runbook

Dopo aver importato i runbook, è necessario aggiungere un webhook al runbook in modo che possa essere attivato da un avviso da una macchina virtuale. Informazioni dettagliate sulla creazione di un webhook per il runbook sono disponibili nell'articolo seguente:

* [Webhook di Automazione di Azure](../automation/automation-webhooks.md)

Assicurarsi di copiare il webhook prima di chiudere la finestra di dialogo del webhook, in quanto sarà necessario nella sezione successiva.

## Aggiungere un avviso alla macchina virtuale

1. Selezionare le impostazioni della macchina virtuale
2. Selezionare "Regole di avviso"
3. Selezionare "Aggiungi avviso"
4. Selezionare una metrica per attivare l'avviso
5. Selezionare una condizione che, se soddisfatta, farà generare l'avviso
6. Selezionare una soglia affinché la condizione nel passaggio 5 sia soddisfatta.
7. Selezionare un periodo in cui il servizio di monitoraggio verificherà la condizione e la soglia dei passaggi 5 e 6
8. Incollare il webhook copiato dalla sezione precedente

![Aggiunta di un avviso alla macchina virtuale 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Aggiunta di un avviso alla macchina virtuale 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)

<!---HONumber=AcomDC_0824_2016-->