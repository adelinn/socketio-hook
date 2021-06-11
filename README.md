# socketio-hook
Just paste this code in the console or use an extension such as Tampermonkey to auto-inject it.

`WebSocket.prototype._send = WebSocket.prototype.send;
    WebSocket.prototype.send = function(data, ...otherData) {
        window.ws = this;
        console.log('%c Sent:', 'color: #bada55', data, ...otherData);
        this._send(data, ...otherData);
        this.send = function(_data, ..._otherData) {
            if (_data != "2probe" && _data != "2" && _data != "5") console.log('%c Sent:', 'color: #bada55', _data, ..._otherData);
            this._send(_data, ..._otherData);
        };
        this._onmessage = this.onmessage;
        this.onmessage = function(_data) {
            if (_data.data != "3probe" && _data.data != "3") {
                let opCode = parseInt(_data.data, 10) + "",
                    jsonData = _data.data.slice(opCode.length);
                setTimeout(() => {
                    console.log('%c Got:', 'color: #fa5a55', opCode, JSON.parse(jsonData ? jsonData : "[]"));
                }, 1);
            }
            this._onmessage(_data);
        };
    };
`
