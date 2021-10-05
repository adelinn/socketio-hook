# socketio-hook
Just paste this code in the console or use an extension such as Tampermonkey to auto-inject it.

```javascript
    WebSocket.prototype._send = WebSocket.prototype.send;
    WebSocket.prototype.send = async function(data, ...otherData) {
        if(this.url.indexOf("socket.io")===-1) {
            this._send(data, ...otherData);
            this.send = this._send;
            return;
        }
        window.ws = this;
        console.log('%c Sent:', 'color: #bada55', data, ...otherData);
        this._send(data, ...otherData);
        this.send = async function(_data, ..._otherData) {
            if(_data != "2probe" && _data != "2" && _data != "5") console.log('%c Sent:', 'color: #bada55', _data, ..._otherData);
            await this._send(_data, ..._otherData);
        };
        this._onmessage = this.onmessage;
        this.onmessage = function(_data) {
            if (_data.data != "3probe" && _data.data != "3") {
                let delim = _data.data.indexOf("["), jsonData = _data.data.slice(delim);
                try {
                    var parsed = JSON.parse(jsonData ? jsonData : "[]");
                    console.log('%c Got:', 'color: #fa5a55', parseInt(_data.data,10), parsed);
                    if(parsed[0].stack) console.error(parsed[0].stack);
                    else if(parsed[0].message) console.error((parsed[0].name?parsed[0].name:"UnknownError")+": "+parsed[0].message);
                    window.lastResponse=JSON.parse(jsonData ? jsonData : "[]");
                } catch(e) {};
            }
            this._onmessage(_data);
        };
    };
```
