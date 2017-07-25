#### Per arrestare e avviare un'appliance cloud
<a id="to-stop-and-start-a-cloud-appliance" class="xliff"></a>

1. Per arrestare un'appliance cloud, passare alla relativa macchina virtuale.
    ![Macchina virtuale appliance cloud StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Nella barra dei comandi fare clic su **Arresta**.

    ![Macchina virtuale appliance cloud StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Alla richiesta di conferma fare clic su **Sì**.

    ![Macchina virtuale appliance cloud StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Con l'arresto, la macchina virtuale viene deallocata. Durante l'arresto dell'appliance cloud, il suo stato è **Deallocazione**. Dopo l'arresto dell'appliance cloud, il suo stato è **Arrestato (deallocato)**.

    ![Macchina virtuale appliance cloud StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Dopo l'arresto della macchina virtuale, fare clic su **Avvia** (il pulsante diventa disponibile) per avviare la macchina virtuale. Dopo l'avvio dell'appliance cloud, il suo stato è **Avviato**.

    ![Macchina virtuale appliance cloud StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Usare i cmdlet seguenti per arrestare e avviare un'appliance cloud.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### Per riavviare un'appliance cloud
<a id="to-restart-a-cloud-appliance" class="xliff"></a>

Per riavviare un'appliance cloud, passare alla relativa macchina virtuale. Nella barra dei comandi fare clic su **Riavvia**. Quando richiesto, confermare il riavvio. Quando l'appliance cloud è pronta per l'uso, lo stato viene indicato come **In esecuzione**.

![Macchina virtuale appliance cloud StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Usare il cmdlet seguente per riavviare un'appliance cloud.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

