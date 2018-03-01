# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate

Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze di macchina virtuale riservate. Per altre informazioni, vedere l'articolo relativo all'[offerta delle istanze di macchina virtuale riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/).

È possibile acquistare le istanze di macchina virtuale riservate nel [portale di Azure](https://portal.azure.com). Per acquistare un'istanza di macchine virtuali riservata:
-   È necessario disporre del ruolo di Proprietario per almeno una sottoscrizione aziendale o con pagamento in base al consumo.
-   Per le sottoscrizioni aziendali gli acquisti di prenotazioni devono essere abilitati nel [portale EA](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>Acquistare un'istanza di macchine virtuali riservata
1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** per acquistare una nuova prenotazione.
4. Compilare i campi obbligatori. Lo sconto relativo alla prenotazione viene applicato alle istanze di macchine virtuali in esecuzione che corrispondono agli attributi. Il numero di istanze di macchine virtuali a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

    | Campo      | DESCRIZIONE|
    |:------------|:--------------|
    |NOME        |Il nome della prenotazione.| 
    |Sottoscrizione|La sottoscrizione usata per pagare la prenotazione. L'acquisto delle istanze di macchina virtuale riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta: MS-AZR-0017P) o con pagamento in base al consumo (numero offerta: MS-AZR-0003P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.|    
    |Scope       |L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>Sottoscrizione singola: lo sconto relativo alla prenotazione viene applicato alle macchine virtuali in questa sottoscrizione. </li><li>Condivisa: lo sconto relativo alla prenotazione viene applicato alle macchine virtuali in tutte le sottoscrizione all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni (eccetto le sottoscrizioni di sviluppo/test) all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</li></ul>|
    |Località    |L'area di Azure coperta dalla prenotazione.|    
    |Dimensioni macchina virtuale     |Le dimensioni delle istanze della macchina virtuale.|
    |Termine        |Un anno o tre anni.|
    |Quantità    |Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze di macchina virtuale in esecuzione che possono ottenere lo sconto sulla fatturazione. Ad esempio, se si eseguono 10 macchine virtuali Standard_D2 negli Stati Uniti orientali, si specificherà 10 come quantità per ottenere lo sconto per tutte le macchine in esecuzione. |
5. È possibile visualizzare il costo della prenotazione quando si seleziona **Calcola costo**.

    ![Schermata prima di inviare l'acquisto della prenotazione](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Selezionare **Acquisto**.
7. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

    ![Schermata prima di inviare l'acquisto della prenotazione](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Passaggi successivi 
Lo sconto della prenotazione si applica automaticamente alle macchine virtuali in esecuzione corrispondenti all'ambito di prenotazione e agli attributi. È possibile aggiornare l'ambito della prenotazione nel [portale di Azure](https://portal.azure.com), in PowerShell, nell'interfaccia della riga di comando o tramite le API. 

Per informazioni su come gestire una prenotazione, vedere [Gestire le istanze di macchina virtuale riservate di Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Per altre informazioni sulle istanze di macchine virtuali riservate, vedere gli articoli seguenti.

- [Risparmiare sui costi delle macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../articles/billing/billing-save-compute-costs-reservations.md)
- [Informazioni su come viene applicato lo sconto relativo alle istanze di macchine virtuali riservate](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle istanze riservate](../articles/billing/billing-reserved-instance-windows-software-costs.md)