# Workaroud for phonegap-plugin-barcodescanner navigation bug

The issue is when you want to cancel reading a barcode then pressing the hardware back button
not only returns from barcode reading screen but navigates back once.

Corresponding [bug on Github](https://github.com/phonegap/phonegap-plugin-barcodescanner/issues/717)

## Workaround for Ionic 5

1. cancel backButton action before opening barcodescanner
2. restore function after finishing

```ts
import { BarcodeScannerOptions, BarcodeScanner } from '@ionic-native/barcode-scanner/ngx';
import { Subscription } from 'rxjs';

private preventBack: Subscription;

scan() {
    this.preventBack = this.platform.backButton.subscribeWithPriority(9999, () => {});
    this.barcodeScanner.scan(this.barcodeScannerOptions).then((barcodeData) => {
        console.log(barcodeData);
        if (barcodeData.cancelled) {
            return;
        }
        // process result here

    }, (err) => {
        console.error(err);
    }).finally(() => {
        window.setTimeout(() => {
            this.preventBack.unsubscribe();
        }, 1000);
    });
}

```