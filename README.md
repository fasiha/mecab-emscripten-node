# mecab-emscripten-node
Run MeCab with just Node. No C++ compilation required, all JavaScript and WebAssembly (wasm), courtesy of Emscripten.

> (You do have to provide your own dictionaries though.)
>
> > (This package as is won't help you with browsers, but others might be able to help: [1](https://github.com/fasiha/mecab-emscripten), [2](https://github.com/Birch-san/mecab-native).)
> >
> > > (And if you have no idea what the heck I'm talking about, well, are you in for a treat: MeCab is a venerable morphological parser and part-of-speech tagger for Japanese text. And if those words mean nothing, maybe this will get you started: ["How to Tokenize Japanese in Python"](https://www.dampfkraft.com/nlp/how-to-tokenize-japanese.html).)

## Install
Install this package from GitHub:
```console
npm i https://github.com/fasiha/mecab-emscripten-node
```
Then use it as a drop-in replacement for MeCab:
```console
# Default: IPADic
$ echo '私は田中です' | npx mecab-emscripten-node
私	名詞,代名詞,一般,*,*,*,私,ワタシ,ワタシ
は	助詞,係助詞,*,*,*,*,は,ハ,ワ
田中	名詞,固有名詞,人名,姓,*,*,田中,タナカ,タナカ
です	助動詞,*,*,*,特殊・デス,基本形,です,デス,デス
EOS

## UniDic
$ echo '私は田中です' | npx mecab-emscripten-node -d /usr/local/lib/mecab/dic/unidic
私	ワタクシ	ワタクシ	私-代名詞	代名詞
は	ワ	ハ	は	助詞-係助詞
田中	タナカ	タナカ	タナカ	名詞-固有名詞-人名-姓
です	デス	デス	です	助動詞	助動詞-デス	終止形-一般
```

## Dev
Install [Emscripten](https://emscripten.org/docs/getting_started/downloads.html) or just launch the official Docker image—install Docker for your OS and then run:
```console
docker run -it --rm -v $(pwd):$(pwd) -u $(id -u):$(id -g) emscripten/emsdk
```

Once you have Emscripten (i.e., `emcc -v` works):
```console
git clone https://github.com/taku910/mecab
cd mecab/mecab
emconfigure ./configure
emmake make VERBOSE=1 -j3
cd src/.libs/
emcc ../mecab.o libmecab.so -o mecab.js -s NODERAWFS=1 -s ALLOW_MEMORY_GROWTH=1 -O3
```
The resulting `mecab.js` and `mecab.wasm` are what's included in this repo (with the addition of `#!/usr/bin/env node` at the top of the former).

## Performance
I've done no proper benchmarks but, very roughly, there seems to be a 300–500 millisecond upfront cost in initializing Node and parsing the wasm payload. After that, qualitative throughput seems to be on par with natively-compiled MeCab.
