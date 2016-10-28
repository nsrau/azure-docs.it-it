#### Per arrestare e avviare un dispositivo virtuale
Per arrestare un dispositivo virtuale, fare clic sul suo nome, quindi selezionare **Arresta**. Durante l'arresto del dispositivo virtuale, lo stato viene indicato come **Arresto in corso**. Dopo aver arrestato il dispositivo virtuale, lo stato viene indicato come **Arrestato**.

Utilizzare i cmdlet seguenti per arrestare e avviare un dispositivo virtuale.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### Per riavviare un dispositivo virtuale

Quando un dispositivo virtuale è in esecuzione e si desidera riavviarlo, fare clic sul suo nome, quindi scegliere **Riavvia**. Durante il riavvio, lo stato del dispositivo viene indicato come **Riavvio in corso**. Quando è possibile utilizzare il dispositivo, lo stato viene indicato come **In esecuzione**.

Utilizzare i cmdlet seguenti per riavviare un dispositivo virtuale.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

<!---HONumber=AcomDC_1217_2015-->