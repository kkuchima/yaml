## 【備忘】DockerのENTRYPOINT/CMDとKubernetesのcomand/argsの違い

### Docker
docker image からdocker container を実行するときにCMD やENTRYPOINT の記述内容が実行される  
1つのDockerfile にCMD、ENTRYPOINT は1つ記載する。複数記載されている場合は、最後の1個が実行される  
ENTRYPOINTには基本的に書き換える必要のない部分を定義しておき、CMDの部分にデフォルトの引数を定義するというのが一般的な使い方  

---

### ENTRYPOINTとCMDの違い
Dockerfile内でCMDのみ定義されている場合、docker run時に実行プロセスを指定した際にCMDで定義した内容を上書きする動きとなる  
Dockerfile内でENTRYPOINTを定義している場合、 CMD と違い、docker run 時にコマンドの上書きができない*  
Dockerfile内でENTRYPOINTとCMDを定義している場合、CMDはENTRYPOINTの引数として実行される
※`docker run - entrypoint=`でENTRYPOINTの上書きすることは可能  

---

### ENTRYPOINTとCMDの記載方法による挙動の違い
#### ENTRYPOINT
ENTRYPOINT ["executable", "param1", "param2"]… シェルを介さずに実行(推奨)  
ENTRYPOINT command param1 param2… /bin/sh -c の引数として実行  

#### CMD
CMD ["executable","param1","param2"]… シェルを介さずに実行(推奨)  
CMD ["param1","param2"]… ENTRYPOINTの引数として実行  
CMD command param1 param2… /bin/sh -c の引数として実行  

---

### Kubernetes
KubernetesのcommandはDockerでいうENTRYPOINT  
KuberntesのargsはDockerでいうCMD  