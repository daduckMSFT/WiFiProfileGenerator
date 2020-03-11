# Functions Overview

## Table of contents

- [Brief Overview](#brief-overview)
- [Reading text input](#reading-text-input)
- [ChangeValue function](#changevalue-function)
  - [Simple Example](#simple-changevalue-example)
  - [Complex ChangeValue example](#complex-changevalue-example)
- [Miscellanious functions](#miscellanious-functions)
  - [Toggling the passphrase input](#toggling-the-passphrase-input)
  - [Open Security function](#open-security-type)
  
## Brief Overview

[Back to top](#table-of-contents)

Brief overview of what happens (very high level) - the input you enter into the fields (eg; SSID) or the buttons you tick, modify the default text in the "Generated Profile" at the bottom.
The profile at the bottom is held in a set of two elements:
```html<pre>``` and ```html<code>```. This keeps the pre-existing formatting needed.
The contents inside of it are a default one I exported from a Windows machine. I added some simple ```html<span>``` elements to modify the text.
    There are spans (such as ```html<span id="outputSSID">Sample SSID</span>```that I identify with an ID - aka, **outputSSID**.
These are changed in real-time with some javascript functions that run in the browser. See below for more...

There are (2) different methods I took to make these functions. Everything has to happen client-side, so it is done in real-time.
**This is because Wi-Fi SSIDs and Passphrases are obviously sensitive material - and I don't want it, and you don't want anyone else having it!**

## Reading text input

[Back to top](#table-of-contents)

Let's begin - the basics here, and something required of nearly every network..
The SSID name, and hopefully a pre-shared key / passphrase!

My goal was to gather this information, and parse it all locally within the browser.
I used JQuery for the majority of this. It also had to convert, for this Android & Windows portion, the name into a hexadecimal key.

We have two functions that are used for the text input:

- **toHex**

    ```javascript
    function toHex(input) {
            var result = "";
                for (i=0; i<input.length; i++) {
                    hex = input.charCodeAt(i).toString(16);
                    result += (hex).slice(-4);
                }
            return result
            };
            function readHex() {
                $( "#inputSSID" )
                .keyup(function() {
                var color = 'Aqua';
                var value = $( this ).val();
                outputvalue = toHex(value);
                $( "#outputSSIDHex" ).text( outputvalue );
                $( "#outputSSIDHex" ).css('color', color );
                });
            }
    ```

- **inputText**

    ```javascript
        function inputText(input,output,output2=0) {
          $( input )
          .keyup(function(){
            var value = $( input ).val();
            $( output ).text( value );
            $( output ).css('color', colorAqua );
            if (output2 != 0) {
              $( output2 ).text( value );
              $( output2 ).css('color', colorAqua );
            } else {
              return;
            }
          });
        };
    ```

1. **readHex** is hard coded (see above) to pull the input of the text input (id of inputSSID, aka #inputSSID) and output the converted hex, without spaces
2. **inputText** can receive three parameters - input, output, and output2.
    You can pass through the fields for up to (3) parameters.
    1. The first one is the input (the form you type the data into).
    2. The second one is the output element ID (where the data you enter is written to)
    3. The third one is the optional output (if it writes to the same field twice - the "InputSSID" writes to both a "Network Name" and "SSID" span element at the bottom, for example)

***These are called at the top of the page, to ensure that your browser is constantly reading the inputText of the needed fields, and converting to Hex***

```javascript
    <script>
      $(document).ready(function(){
        readHex();
        inputText("#inputSSID","#outputSSID","#outputNetworkName");
        inputText("#inputPassphrase","#outputPassphrase");
      });
    </script>
```

This essentially monitors the inputSSID field and sets it to output to the outputSSID and outputNetworkName span at the bottom.
It also monitors the inputPassphrase field, and puts the output into the outputPassphrase span.

## ChangeValue function

[Back to top](#table-of-contents)

Next up, the buttons (to change Security, Hidden, Automatically Connect, etc).
By default, these fields are checked - and do nothing. However, clicking them calls an "onclick" element to call a specific function seen below called "changeValue"

```javascript
        function changeValue(destination,value,color,destination2=0,value2=0) {
          var dest = document.getElementById(destination);

          if (color == 'aqua') {
            var color = colorAqua;};

          if (color == 'white') {
            var color = colorWhite;};

          if (destination2 != 0) {
            var dest2 = document.getElementById(destination2);};

          $( dest ).text( value );
          $( dest ).css('color', color);
          if (dest2 != null) {
            $( dest2 ).text( value2 );
            $( dest2 ).css('color', color);};
        };
```

changeValue accepts (5) parameters - (3) of which are optional.

1. *destination* - what is the span we want to edit in the bottom?
2. *value* - what are we setting in the span (and the generated profile)?
3. *color* - do we want it to be the aqua color (aka, are we changing the default value) or do we want it to be white?

optional:

1. *destination2* - is there a second field we need to modify? (eg; setting WPA / WPA2 also change the 'outputEncryption' span)
2. *value2* - what are we putting into the second span? (eg; we're putting outputEncryption, specified above, to 'AES')

dest2 and value2 are "0" by default. I have some basic checking to use them when needed.

### Simple ChangeValue example

[Back to top](#table-of-contents)

Example of a simple "changeValue" function call:
    When you select that 'Yes', it IS a 'Hidden' network, it would look like this:
    ```html<input class="form-check-input" type="radio" name="hiddenRadios" id="inputHiddenYes" value="option2" onclick="changeValue('outputHidden','true','aqua')">```
    This calls: ```htmlonclick="changeValue('outputHidden','true','aqua')"```

This sends the **changeValue** function the parameter of *outputHidden* , so that's the span we're modifying in the generated profile.
We set the value to *true*
Since we're modifying it, we set the color to **aqua**

- If we were setting it back to NOT be hidden, we would set the second parameter to *false* instead of *true*, and the color to *white*

### Complex ChangeValue example

[Back to top](#table-of-contents)

Example of a tad more complex "changeValue" function call..

When you select the button to use 'WPA'
```html onclick="changeValue('outputSecurityType','WPAPSK','aqua','outputEncryption','AES')"```

This sends a call to **changeValue** that tells us to:

1. Modify the ***outputSecurityType*** span and put the text **'WPAPSK'** in there with **aqua** text specified in parameter 3
2. Modify the ***outputEncryption*** span and put the text **'WPAPSK'** in there with the **aqua** text specified in parameter 3

So - these are the basics of changing value. the ChangeValue function is used for everything that isn't a text box!

## Miscellanious functions

[Back to top](#table-of-contents)

Next up, the functions that can be used a bit if you want to modify this...

### Toggling the passphrase input

[Back to top](#table-of-contents)

```javascript
        function passphraseEnableDisable(input) {
          var disabledState = $( '#inputPassphrase' ).is( ":disabled" );
          var passphraseInput = $( '#inputPassphrase' );
          var passphraseOutput = $( '#outputPassphrase' );

          if (input == 'disable') {
            passphraseInput.prop( "value", "");
            passphraseOutput.prop ( "textContent", "");
            passphraseInput.prop( "disabled", true);
            };

          if ((disabledState = true) & (input == 'enable')) {
            passphraseInput.prop( "disabled", false);
            };
        };
```

This function is simple - if you call it (using something such as ```html onclick="passphraseEnableDisable('enable')">```
It will simply set the element form for **inputPassphrase** to not be **disabled**.

If you use **disable**, it will clear the existing input, output, and then disable the element.
There is some checking in here - the second if loop - to ensure that if you are on 'WPA' and you switch to 'WPA2' it doesn't clear your Input.

## Open Security Type

[Back to top](#table-of-contents)

There is an "openSecurity" function...

```javascript
        function openSecurity() {
          changeValue('outputSecurityType','open','white','outputEncryption','none');
          passphraseEnableDisable('disable');};
```

By default, the "Open" security type is checked/selected. There is no need to use a Passphrase with this, so we have the passphrase form disabled by default.

This is called via the following html element: ```htmlonclick="openSecurity()"```

I would normally keep this as a single function, but this allows granularity if I want to change behavior.

When the button is clicked, it calls the **openSecurity** function, which then calls...

1. ```javascript changeValue('outputSecurityType','open','white','outputEncryption','none');```
    1. This has it modify the **openSecurityType** span (the value of '*open*') and the **outputEncryption** span with '*none*'
    It sets both to use white text as these are the default values I've provided.
2. ```javascript passphraseEnableDisable('disable');```
    1. It then calls the **passphraseEnableDisable** function, with the parameter of '**disable**' - which disables the input, and clears all of the input / output text.

Pardon if there are any glaringly obvious mistakes here - I'm not a web developer, but this was fun to make!
