#### Linux bash 之花括号用法小结--Brace Expansion

###### 背景说明

Linux shell 脚本中的花括号用法真是五花八门，姿势之多只能是使人琳琅满目、瞠目结舌，手摸键盘而望洋兴叹，WC! 还能这么用，这也太TM骚了，文字不足以表达对前辈的敬意，聊表数字记录，主要是梳理下常见用法以防忘记。

* 变量边界
  受限于shell脆弱的变量约束机制（变相也带来的意向不到的灵活性--也可能是灾难性的），借助大括号约束shell变量的范围，避免歧义
  
  ```bash
  root@heimdall:~# var=50
  root@heimdall:~# var1=100
  root@heimdall:~# var2=200
  root@heimdall:~# echo $var1 ${var}1
  100 501
  root@heimdall:~# echo $var2 ${var}2
  200 502
  ```

* 逗号扩展--常用于备份或者压缩
  *逗号扩展要求花括号里至少有一个逗号*
  
  ```bash
  #将花括号里的每一项展开 然后与前后结合
  root@heimdall:~# echo a{d,c,b}e
  ade ace abe
  #备份
  root@heimdall:~# cp /etc/apt/sources.list{,.bak}
  root@heimdall:~# ls /etc/apt/sources.list
  sources.list      sources.list~     sources.list.bak  sources.list.d/  
  #因为逗号前边的为空 所有展开直接等效于
  root@heimdall:~# cp /etc/apt/sources.list /etc/apt/sources.list.bak 
  #还原
  root@heimdall:~# cp /etc/apt/sources.list{.bak,}
  #这次逗号后边为空 所以等效于
  root@heimdall:~# cp /etc/apt/sources.list.bak /etc/apt/sources.list
  #类似的备份目录
  root@heimdall:~# mv all-images{,-bak}
  #等效于
  root@heimdall:~# mv all-images all-images-bak
  #压缩的时候--这样不用输入两遍文件名了
  root@heimdall:~# tar -zcvf go2tencentcloud{.tgz,}
  ```

* 占位符
  与find搭配使用，查找当前目录文件类型并列出，这里的花括号“{}”代表占位符，占位占的是前面查出来的文件 
  
  ```bash
  find . -type f -exec ls -l {} \;
  ```

* 序列--或者说枚举
  参考：[技术|浅析 Bash 中的 {花括号} (linux.cn)](https://linux.cn/article-10624-1.html)
  
  开始位..结束位..步长
  
  步长必须为数字
  
  小写a应该是比大写Z的值要大 应该从大写A开始
  大小写之间有 [  ] ^ _ ` 四个特殊字符--这是因为花括号扩展是按照ASCII码表的顺序输出的
  
  ```BASH
  root@heimdall:~# echo {1..26}
  1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26
  root@heimdall:~# echo {1..26..2}
  1 3 5 7 9 11 13 15 17 19 21 23 25
  root@heimdall:~# echo {1..26..3}
  1 4 7 10 13 16 19 22 25
  root@heimdall:~# echo {a..z}
  a b c d e f g h i j k l m n o p q r s t u v w x y z
  root@heimdall:~# echo {a..z..2}
  a c e g i k m o q s u w y
  root@heimdall:~# echo {a..z..3}
  a d g j m p s v y
  root@heimdall:~# echo {a..Z..3}
  a ^ [
  root@heimdall:~# echo {a..Z}
  a ` _ ^ ]  [ Z
  root@heimdall:~# echo {A..Z}
  A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
  root@heimdall:~# echo {A..z}
  A B C D E F G H I J K L M N O P Q R S T U V W X Y Z [  ] ^ _ ` a b c d e f g h i j k l m n o p q r s t u v w x y z
  #有空格
  root@heimdall:~# echo {a..z} {A..Z}
  a b c d e f g h i j k l m n o p q r s t u v w x y z A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
  #无空格 为两者所有的排列组合
  root@heimdall:~# echo {a..z}{A..Z}
  aA aB aC aD aE aF aG aH aI aJ aK aL aM aN aO aP aQ aR aS aT aU aV aW aX aY aZ bA bB bC bD bE bF bG bH bI bJ bK bL bM bN bO bP bQ bR bS bT bU bV bW bX bY bZ cA cB cC cD cE cF cG cH cI cJ cK cL cM cN cO cP cQ cR cS cT cU cV cW cX cY cZ dA dB dC dD dE dF dG dH dI dJ dK dL dM dN dO dP dQ dR dS dT dU dV dW dX dY dZ eA eB eC eD eE eF eG eH eI eJ eK eL eM eN eO eP eQ eR eS eT eU eV eW eX eY eZ fA fB fC fD fE fF fG fH fI fJ fK fL fM fN fO fP fQ fR fS fT fU fV fW fX fY fZ gA gB gC gD gE gF gG gH gI gJ gK gL gM gN gO gP gQ gR gS gT gU gV gW gX gY gZ hA hB hC hD hE hF hG hH hI hJ hK hL hM hN hO hP hQ hR hS hT hU hV hW hX hY hZ iA iB iC iD iE iF iG iH iI iJ iK iL iM iN iO iP iQ iR iS iT iU iV iW iX iY iZ jA jB jC jD jE jF jG jH jI jJ jK jL jM jN jO jP jQ jR jS jT jU jV jW jX jY jZ kA kB kC kD kE kF kG kH kI kJ kK kL kM kN kO kP kQ kR kS kT kU kV kW kX kY kZ lA lB lC lD lE lF lG lH lI lJ lK lL lM lN lO lP lQ lR lS lT lU lV lW lX lY lZ mA mB mC mD mE mF mG mH mI mJ mK mL mM mN mO mP mQ mR mS mT mU mV mW mX mY mZ nA nB nC nD nE nF nG nH nI nJ nK nL nM nN nO nP nQ nR nS nT nU nV nW nX nY nZ oA oB oC oD oE oF oG oH oI oJ oK oL oM oN oO oP oQ oR oS oT oU oV oW oX oY oZ pA pB pC pD pE pF pG pH pI pJ pK pL pM pN pO pP pQ pR pS pT pU pV pW pX pY pZ qA qB qC qD qE qF qG qH qI qJ qK qL qM qN qO qP qQ qR qS qT qU qV qW qX qY qZ rA rB rC rD rE rF rG rH rI rJ rK rL rM rN rO rP rQ rR rS rT rU rV rW rX rY rZ sA sB sC sD sE sF sG sH sI sJ sK sL sM sN sO sP sQ sR sS sT sU sV sW sX sY sZ tA tB tC tD tE tF tG tH tI tJ tK tL tM tN tO tP tQ tR tS tT tU tV tW tX tY tZ uA uB uC uD uE uF uG uH uI uJ uK uL uM uN uO uP uQ uR uS uT uU uV uW uX uY uZ vA vB vC vD vE vF vG vH vI vJ vK vL vM vN vO vP vQ vR vS vT vU vV vW vX vY vZ wA wB wC wD wE wF wG wH wI wJ wK wL wM wN wO wP wQ wR wS wT wU wV wW wX wY wZ xA xB xC xD xE xF xG xH xI xJ xK xL xM xN xO xP xQ xR xS xT xU xV xW xX xY xZ yA yB yC yD yE yF yG yH yI yJ yK yL yM yN yO yP yQ yR yS yT yU yV yW yX yY yZ zA zB zC zD zE zF zG zH zI zJ zK zL zM zN zO zP zQ zR zS zT zU zV zW zX zY zZ
  #花括号排列组合
  root@heimdall:~# echo {0..1}{0..1}{0..1}{0..1}{0..1}{0..1}{0..1}{0..1}
  00000000 00000001 00000010 00000011 00000100 00000101 00000110 00000111 00001000 00001001 00001010 00001011 00001100 00001101 00001110 00001111 00010000 00010001 00010010 00010011 00010100 00010101 00010110 00010111 00011000 00011001 00011010 00011011 00011100 00011101 00011110 00011111 00100000 00100001 00100010 00100011 00100100 00100101 00100110 00100111 00101000 00101001 00101010 00101011 00101100 00101101 00101110 00101111 00110000 00110001 00110010 00110011 00110100 00110101 00110110 00110111 00111000 00111001 00111010 00111011 00111100 00111101 00111110 00111111 01000000 01000001 01000010 01000011 01000100 01000101 01000110 01000111 01001000 01001001 01001010 01001011 01001100 01001101 01001110 01001111 01010000 01010001 01010010 01010011 01010100 01010101 01010110 01010111 01011000 01011001 01011010 01011011 01011100 01011101 01011110 01011111 01100000 01100001 01100010 01100011 01100100 01100101 01100110 01100111 01101000 01101001 01101010 01101011 01101100 01101101 01101110 01101111 01110000 01110001 01110010 01110011 01110100 01110101 01110110 01110111 01111000 01111001 01111010 01111011 01111100 01111101 01111110 01111111 10000000 10000001 10000010 10000011 10000100 10000101 10000110 10000111 10001000 10001001 10001010 10001011 10001100 10001101 10001110 10001111 10010000 10010001 10010010 10010011 10010100 10010101 10010110 10010111 10011000 10011001 10011010 10011011 10011100 10011101 10011110 10011111 10100000 10100001 10100010 10100011 10100100 10100101 10100110 10100111 10101000 10101001 10101010 10101011 10101100 10101101 10101110 10101111 10110000 10110001 10110010 10110011 10110100 10110101 10110110 10110111 10111000 10111001 10111010 10111011 10111100 10111101 10111110 10111111 11000000 11000001 11000010 11000011 11000100 11000101 11000110 11000111 11001000 11001001 11001010 11001011 11001100 11001101 11001110 11001111 11010000 11010001 11010010 11010011 11010100 11010101 11010110 11010111 11011000 11011001 11011010 11011011 11011100 11011101 11011110 11011111 11100000 11100001 11100010 11100011 11100100 11100101 11100110 11100111 11101000 11101001 11101010 11101011 11101100 11101101 11101110 11101111 11110000 11110001 11110010 11110011 11110100 11110101 11110110 11110111 11111000 11111001 11111010 11111011 11111100 11111101 11111110 11111111
  root@VM-16-8-ubuntu:/home/heimdall/app/inSuite# echo {0..1}{0..1}{0..1}{0..1}{0..1}{0..1}{0..1}{0..1} | wc 
        1     256    2304
  ```

* 将多个命令合并在一起--或者for循环体写在一行用来避免循环体里带&后台运行引起的歧义
  
  ```bash
  #将多个命令的输出一起输出重定向到文件实例
  root@heimdall:~# echo "I found all these PNGs:"; find . -iname "*.png"; echo "Within this bunch of files:"; ls > PNGs.txt
  root@heimdall:~# { echo "I found all these PNGs:"; find . -iname "*.png"; echo "Within this bunch of files:"; ls; } > PNGs.txt
  ```
  
  ```
  #循环体写在一行
  IMAGES=($(kubectl get pods --all-namespaces -o jsonpath="{..image}" | tr -s '[[:space:]]' '\n' | sort | uniq | grep -v ^rancher))
  #如果为了验证脚本需要把命令写在一行 注意循环体do的内容用大括号抱起来 不然后台进程& 会被识别报错
  for I in ${IMAGES[@]}; do { TAG=${I##*:};  URL=${I%:*};  IMAGE=${URL//\//_};  ctr i export ${IMAGE}_${TAG}.tar ${I} &}; done
  #等效于如下循环体
  #for I in ${IMAGES[@]}; do 
  #  echo "current image $I"
  #  TAG=${I##*:}
  #  URL=${I%:*}
  #  IMAGE=${URL//\//_}
  #  docker save $I > ${IMAGE}_${TAG}.tar &
  #  ctr i export ${IMAGE}_${TAG}.tar ${I} &
  #done
  ```

* 官方扩展--man bash
  查看man bash会发现很多有关花括号的用法，举例说明
  
  EXPANSION==>   Brace Expansion
  
  ```
                mkdir /usr/local/src/bash/{old,new,dist,bugs}
         or
                chown root /usr/{ucb/{ex,edit},lib/{ex?.?*,how_ex}}
  ```
  
  Parameter Expansion 参数扩展，因为英文说明比较详细且更准确，所以直接使用了，难点部分补充中文说明
  
  * ${parameter:-word}
    Use Default Values.  If parameter is unset or null, the expansion of word is substituted.  Otherwise, the value of parameter is substituted.
  
  * ${parameter:=word}
    
    Assign Default Values.  If parameter is unset or null, the expansion of word is assigned to parameter.  The value of parameter is then substituted.   Positional  parameters  and  special
    parameters may not be assigned to in this way.
    与前者不同的是，如果参数parameter 为设置或者为空的话，那么直接将word的值复制给parameter而不是使用word的值，这样以后parameter就有值了
  
  * ${parameter:?word}
    
    Display Error if Null or Unset.  If parameter is null or unset, the expansion of word (or a message to that effect if word is not present) is written to the standard error and the shell, if it is not interactive, exits.  Otherwise, the value of parameter is substituted.
    word一般表示为错误反馈信息如："parameter  if Null or Unset"
  
  * ${parameter:+word}
    Use Alternate Value.  If parameter is null or unset, nothing is substituted, otherwise the expansion of word is substituted.
    与第一个相反，如果parameter未设置或者为空，那么啥都不变，但是 但凡parameter设置了或者不为空那么就去word的值
  
  * ${parameter:offset}
  
  * ${parameter:offset:length}
    切片，从offset处切，默认切到最后如果不加length的话 offset从0开始计数
    
    ```bash
    root@VM-16-8-ubuntu:~# parameter=0123456789
    root@VM-16-8-ubuntu:~# echo ${parameter:5}
    56789
    root@VM-16-8-ubuntu:~# parameter=abcdefg
    root@VM-16-8-ubuntu:~# echo ${parameter:5}
    fg
    root@VM-16-8-ubuntu:~# echo ${parameter:5:1}
    f
    root@VM-16-8-ubuntu:~# echo ${parameter:5:2}
    fg
    ```
    
    如果parameter为@或者*的话，表示数组的下标，两者等效
    
    ```BASH
    #这个是数组
    root@VM-16-8-ubuntu:~# set -- 1 2 3 4 5 6 7 8 9
    root@VM-16-8-ubuntu:~# echo ${@:5:2}
    5 6
    root@VM-16-8-ubuntu:~# echo ${@:0}
    -bash 1 2 3 4 5 6 7 8 9
    root@VM-16-8-ubuntu:~# echo ${@:0:1}
    -bash
    root@VM-16-8-ubuntu:~# echo ${@:1}
    1 2 3 4 5 6 7 8 9
    root@VM-16-8-ubuntu:~# echo ${*:1}
    1 2 3 4 5 6 7 8 9
    root@VM-16-8-ubuntu:~# echo ${*:1:5}
    1 2 3 4 5
    ```
    
    参考：https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html
    
    ```bash
    #############字符串部分##############
    root@VM-16-8-ubuntu:~# string=01234567890abcdefgh
    #切片 切片保留从关键字开始一直到最后 如果不加切片长度的话
    root@VM-16-8-ubuntu:~# echo ${string:7}
    7890abcdefgh
    #切片 切从关键字7开始到最后 然后从开头切0个
    root@VM-16-8-ubuntu:~# echo ${string:7:0}
    
    #切片 切从关键字7开始到最后 然后从开头切2个
    root@VM-16-8-ubuntu:~# echo ${string:7:2}
    78
    #切片 切从关键字7开始到最后 然后再把前边内容尾部再切2个
    root@VM-16-8-ubuntu:~# echo ${string:7:-2}
    7890abcdef
    
    #切片 关键字只能为一个字符 多个字符成功切片输出
    root@VM-16-8-ubuntu:~# echo ${string:789}
    
    root@VM-16-8-ubuntu:~# echo ${string:78}
    
    root@VM-16-8-ubuntu:~# echo ${string:7}
    7890abcdefgh
    #切片 从尾部切 切关键字'7'个 关键字(数字)前边有空格
    root@VM-16-8-ubuntu:~# echo ${string: -7}
    bcdefgh
    #切片 如果关键数字前边没有空格 直接全部输出
    root@VM-16-8-ubuntu:~# echo ${string:-7}
    01234567890abcdefgh
    #切片 从尾部切7个 保留0个
    root@VM-16-8-ubuntu:~# echo ${string: -7:0}
    
    #切片 从尾部切7个 保留左侧的两个
    root@VM-16-8-ubuntu:~# echo ${string: -7:2}
    bc
    #切片 从尾部切7个 然后再把这7个的尾部2个切了
    root@VM-16-8-ubuntu:~# echo ${string: -7:-2}
    bcdef
    ```

    ##############位置参数部分##############
    root@VM-16-8-ubuntu:~# set -- 01234567890abcdefgh
    root@VM-16-8-ubuntu:~# echo ${1:7} #根据下边推断 这就是把第一个参数前边7个切了
    7890abcdefgh
    root@VM-16-8-ubuntu:~# echo ${0:7}
    
    root@VM-16-8-ubuntu:~# echo ${2:7}
    
    root@VM-16-8-ubuntu:~# echo ${3:7}
    
    root@VM-16-8-ubuntu:~# echo ${0}
    -bash
    #实际上--后边的一堆为set的位置参数 第1个位置参数
    root@VM-16-8-ubuntu:~# echo ${1}
    01234567890abcdefgh
    root@VM-16-8-ubuntu:~# echo ${2}
    
    root@VM-16-8-ubuntu:~# echo ${1:2}
    #对第1个位置参数开始切片 关键字2 数字2 表示且左侧的2个 保留后边内容
    234567890abcdefgh
    #第2个必须为数字 若为字幕不起作用
    root@VM-16-8-ubuntu:~# echo ${1:a}
    01234567890abcdefgh
    root@VM-16-8-ubuntu:~# echo ${1:3}
    34567890abcdefgh
    #切0个
    root@VM-16-8-ubuntu:~# echo ${1:0}
    01234567890abcdefgh
    #切片 第1个参数 切左侧7个 保留剩余部分
    root@VM-16-8-ubuntu:~# echo ${1:7}
    7890abcdefgh
    #切片 第一个参数 切左侧7个 从左侧保留0个
    root@VM-16-8-ubuntu:~# echo ${1:7:0}
    
    #切片 第一个参数 切左侧7个 从左侧保留2个
    root@VM-16-8-ubuntu:~# echo ${1:7:2}
    78
    #切片 第一个参数 切左侧7个 再把前者右侧切2个 保留剩余内容
    root@VM-16-8-ubuntu:~# echo ${1:7:-2}
    7890abcdef
    
    root@VM-16-8-ubuntu:~# echo ${1:7}
    7890abcdefgh
    root@VM-16-8-ubuntu:~# echo ${1:7:0}
    
    root@VM-16-8-ubuntu:~# echo ${1:7:2}
    78
    root@VM-16-8-ubuntu:~# echo ${1:7:-2}
    7890abcdef
    #切片 第1个参数 从右侧切7个 保留之
    #--与从左侧切不同 从左侧切 切掉的都丢弃了
    root@VM-16-8-ubuntu:~# echo ${1: -7}
    bcdefgh
    root@VM-16-8-ubuntu:~# echo ${1: -7:0}
    
    root@VM-16-8-ubuntu:~# echo ${1: -7:2} #切片 第1个参数 从右侧切7个 保留之左侧2个
    bc
    root@VM-16-8-ubuntu:~# echo ${1: -7:-2} #切片 第1个参数 从右侧切7个 保留之 然后再将尾部2个丢弃
    bcdef
    root@VM-16-8-ubuntu:~# 
    
    
    
    ##############数组部分##############
    参考位置参数部分 数组同理 示例如下
    root@VM-16-8-ubuntu:~# array[0]=01234567890abcdefgh
    root@VM-16-8-ubuntu:~# echo ${array[0]:7}
    7890abcdefgh
    root@VM-16-8-ubuntu:~# echo ${array[0]:7:0}
    
    root@VM-16-8-ubuntu:~# echo ${array[0]:7:2}
    78
    root@VM-16-8-ubuntu:~# echo ${array[0]:7:5}
    7890a
    root@VM-16-8-ubuntu:~# echo ${array[0]:7:-2}
    7890abcdef
    root@VM-16-8-ubuntu:~# echo ${array[0]: -7}
    bcdefgh
    root@VM-16-8-ubuntu:~# echo ${array[0]: -7:0}
    
    root@VM-16-8-ubuntu:~# echo ${array[0]: -7:2}
    bc
    root@VM-16-8-ubuntu:~# echo ${array[0]: -7:-2}
    bcdef
    root@VM-16-8-ubuntu:~# 
    
    
    ##########################@*####################
    root@VM-16-8-ubuntu:~# set -- 1 2 3 4 5 6 7 8 9 0 a b c d e f g h
    root@VM-16-8-ubuntu:~# echo ${@:7}
    7 8 9 0 a b c d e f g h
    root@VM-16-8-ubuntu:~# echo ${@:7:0}
    
    root@VM-16-8-ubuntu:~# echo ${@:7:2}
    7 8
    root@VM-16-8-ubuntu:~# echo ${@:7:-2}
    -bash: -2: substring expression < 0
    root@VM-16-8-ubuntu:~# echo ${@: -7:2}
    b c
    root@VM-16-8-ubuntu:~# echo ${@:0}
    -bash 1 2 3 4 5 6 7 8 9 0 a b c d e f g h
    root@VM-16-8-ubuntu:~# echo ${@:0:2}
    -bash 1
    root@VM-16-8-ubuntu:~# echo ${@: -7:0}
    
    root@VM-16-8-ubuntu:~# echo ${@: -7}
    b c d e f g h
    
    
    ####数组#####
    root@VM-16-8-ubuntu:~# array=(0 1 2 3 4 5 6 7 8 9 0 a b c d e f g h)
    root@VM-16-8-ubuntu:~# echo ${array[@]:7}
    7 8 9 0 a b c d e f g h
    root@VM-16-8-ubuntu:~# echo ${array[@]:7:2}
    7 8
    root@VM-16-8-ubuntu:~# echo ${array[@]: -7:2}
    b c
    root@VM-16-8-ubuntu:~# echo ${array[@]: -7:-2}
    -bash: -2: substring expression < 0
    root@VM-16-8-ubuntu:~# echo ${array[@]:0}
    0 1 2 3 4 5 6 7 8 9 0 a b c d e f g h
    root@VM-16-8-ubuntu:~# echo ${array[@]:0:2}
    0 1
    root@VM-16-8-ubuntu:~# echo ${array[@]: -7:0}
    
    root@VM-16-8-ubuntu:~# 
    
    ```

* ${!var}
  
  ```bash
  ubuntu@heimdall:~$ var=zhangdeshuai  #将字符串zhangdeshuai复制给变量var
  ubuntu@heimdall:~$ zhangdeshuai=mario  #巧了 字符串也是一个变量 里边存储了一个值mario
  ubuntu@heimdall:~$ echo ${!var}  #那么${!var} 先取第一个变量的值zhangdeshuai 然后又取了一次zhangdeshuai变量的值 结果为mario 有点像eval取变量的值又执行了一遍
  mario
  ubuntu@heimdall:~$ 
  $ hello="this is some text"   # we set $hello
  $ var="hello"                 # $var is "hello"
  $ echo "${!var}"              # we print the variable linked by $var's content
  this is some text
  ```

* ${!prefix*}

* ${!prefix@}
  取变量相同部分的前缀然后用@或者*拼接输出所有匹配的变量名字 而不是变量的值
  
  ```
  ubuntu@heimdall:~$ aaa=1
  ubuntu@heimdall:~$ aab=2
  ubuntu@heimdall:~$ aac=3
  ubuntu@heimdall:~$ echo "${!aa@}"
  aaa aab aac
  ubuntu@heimdall:~$ echo "${!aa*}"
  aaa aab aac
  ubuntu@heimdall:~$ 
  ```

* ${!name[@]}

* ${!name[*]}
  输出的事变量的下表数字号 同样无法输出下一层变量的值
  
  ```
  ubuntu@heimdall:~$ aaa=11111
  ubuntu@heimdall:~$ bbb=22222
  ubuntu@heimdall:~$ ccc=33333
  ubuntu@heimdall:~$ echo ${name[@]}
  aaa bbb ccc
  ubuntu@heimdall:~$ echo ${!name[@]}
  0 1 2
  ubuntu@heimdall:~$ echo ${!name[*]}
  0 1 2
  ubuntu@heimdall:~$ 
  ```

* ${#parameter}
  
      Parameter length.  The length in characters of the value of parameter is substituted.  
      If parameter is * or @, the value substituted is the number of positional parameters.  If parameter is an array name subscripted by * or @, the value substituted is the number of elements in the array.  If parameter is an indexed array name subscripted by a negative number, that number is interpreted as relative to one greater than the maximum index of parameter, so negative indices count back from the end of the array, and an index of -1 references the last element.
  
  ```
  #字符串字符数量
  root@heimdall:~# parameter=nicaicai
  root@heimdall:~# echo ${#parameter}
  8
  root@heimdall:~# 
  #如果是*或者@ 那么为位置参数个数
  root@heimdall:~# set -- 1 2 3 4 5 6 a b c d
  root@heimdall:~# echo {#@}
  {#@}
  root@heimdall:~# echo ${#@}
  10
  root@heimdall:~# echo ${#*}
  10
  root@heimdall:~# set -- 123 asdf hahaha
  root@heimdall:~# echo ${#*}
  3
  root@heimdall:~# echo ${#@}
  3
  root@heimdall:~# 
  #数组情况下
  root@heimdall:~# array=(aaa bbb ccc ddd eee)
  #什么都不加 那么就是第一个元素字符数量
  root@heimdall:~# echo ${#array}
  3
  #@和*情况下 就是数组所有元素的数量
  root@heimdall:~# echo ${#array[@]}
  5
  root@heimdall:~# echo ${#array[*]}
  5
  root@heimdall:~# 
  ```

    ```

* ${parameter#word}

* ${parameter##word}
  **Remove matching prefix pattern.** 
  删除匹配前缀 一个#最短匹配 两个#最长匹配，所谓前缀就是从开头往后匹配
  
  ```
   The word is expanded to produce a pattern just as in pathname expansion.  If the pattern matches the beginning of the value of parameter, then the result of the expansion is the expanded value of parameter with the shortest matching pattern (the ``#'' case) or the longest matching pattern (the ``##'' case) deleted.  If parameter is  @  or *, the pattern removal operation is applied to each positional parameter in turn, and the expansion is the resultant list.  If parameter is an array variable subscripted with @ or *, the pattern removal operation is applied to each member of the array in turn, and the expansion is the resultant list.
  ```

* ${parameter%word}

* ${parameter%%word}
  **Remove matching suffix pattern.**
  删除匹配后缀 一个%最短匹配 两个%最长匹配，所谓后缀就是从尾部往前匹配
  这其中有个关键点 匹配的部分还是从左往右
  其中的另一个关键点 是通配符*号的位置在前缀匹配后缀匹配的时候稍有不同 写脚本时要充分验证
  
  ```
   The word is expanded to produce a pattern just as in pathname expansion.  If the pattern matches a trailing portion of the expanded value  of  parameter,            then  the  result  of  the expansion is the expanded value of parameter with the shortest matching pattern (the ``%'' case) or the longest matching pattern (the ``%%'' case) deleted.  If parameter is @ or *, the pattern removal operation is applied to each positional parameter in turn, and the expansion is the resultant list.  If  parameter  is  an  array  variable  sub‐scripted with @ or *, the pattern removal operation is applied to each member of the array in turn, and the expansion is the resultant list.
  ```

* ${parameter/pattern/string}
  匹配替换

* ${parameter//pattern/string}
  man 手册里少了两个斜线的情况，两个斜线说明全部替换，而一个斜线只替换第一次匹配内容
  另外需要说明的一点是 如果最后的斜线后边没有东西，那么说明就是匹配替换为空，也就是匹配删除

* ${parameter^pattern}
  ${parameter^^pattern}
  ${parameter,pattern}
  ${parameter,,pattern}
  大小写转换 一个符号和两个符号的区别 一个是只转换第一次匹配到的 一个是匹配到的全部转换
  *pattern部分职能为一个字符 多个字符或者通配符的情况下失效*
  
  ```BASH
  root@heimdall:~# parameter=xiaoxiezifuchuan
  #小写转大写 没有指定那么默认转换第一个字符
  root@heimdall:~# echo ${parameter^}
  Xiaoxiezifuchuan
  #没有指定 且两个^的情况下全部转为大写
  root@heimdall:~# echo ${parameter^^}
  XIAOXIEZIFUCHUAN
  #转小写失效
  root@heimdall:~# echo ${parameter,}
  xiaoxiezifuchuan
  #全部转小写失效
  root@heimdall:~# echo ${parameter,,}
  xiaoxiezifuchuan
  root@heimdall:~# 
  #匹配单个字符x 且只匹配一次
  root@heimdall:~# echo ${parameter^x}
  Xiaoxiezifuchuan
  #匹配单个字符x 且全部匹配转换
  root@heimdall:~# echo ${parameter^^x}
  XiaoXiezifuchuan
  #本身是小写 转小写失效
  root@heimdall:~# echo ${parameter,x}
  xiaoxiezifuchuan
  #本身是小写 转小写失效
  root@heimdall:~# echo ${parameter,,x}
  xiaoxiezifuchuan
  root@heimdall:~# 
  root@heimdall:~# parameter=XIAOXIEZIFUCHUAN
  #本身大写 转大写失效
  root@heimdall:~# echo ${parameter^}
  XIAOXIEZIFUCHUAN
  #本身大写 转大写失效
  root@heimdall:~# echo ${parameter^^}
  XIAOXIEZIFUCHUAN
  #转小写 只匹配一次
  root@heimdall:~# echo ${parameter,}
  xIAOXIEZIFUCHUAN
  #转小写 全部匹配且转换
  root@heimdall:~# echo ${parameter,,}
  xiaoxiezifuchuan
  root@heimdall:~# 
  #字符X本身大写 转换失效
  root@heimdall:~# echo ${parameter^X}
  XIAOXIEZIFUCHUAN
  #字符X本身大写 转换失效
  root@heimdall:~# echo ${parameter^^X}
  XIAOXIEZIFUCHUAN
  #字符X本身大写 转换小写 只匹配一次
  root@heimdall:~# echo ${parameter,X}
  xIAOXIEZIFUCHUAN
  #字符X本身大写 转换小写 全部匹配转换
  root@heimdall:~# echo ${parameter,,X}
  xIAOxIEZIFUCHUAN
  #pattern部分职能为一个字符 多个字符或者通配符的情况下失效
  root@heimdall:~# echo ${parameter,,X*O}
  XIAOXIEZIFUCHUAN
  root@heimdall:~# echo ${parameter,,XI}
  XIAOXIEZIFUCHUAN
  root@heimdall:~# 
  ```

* ${parameter/#pattern/string}

* ${parameter/%pattern/string}
  
  ```
  root@heimdall:~# var=abcdef
  root@heimdall:~# rep='& '
  root@heimdall:~# echo ${var/abc/& }
  & def
  root@heimdall:~# echo "${var/abc/& }"
  & def
  root@heimdall:~# echo ${var/abc/$rep}
  & def
  root@heimdall:~# echo "${var/abc/$rep}"
  & def
  root@heimdall:~# var=abcdef
  root@heimdall:~# rep='\\&xyz'
  root@heimdall:~# echo ${var/abc/\\&xyz}
  \&xyzdef
  root@heimdall:~# echo ${var/abc/$rep}
  \\&xyzdef
  root@heimdall:~# 
  ```

* ${parameter@operator}
  operator有5个动作，分别是Q、E、P、A、a，也就是5个字符
  
  ```
                Parameter transformation.  The expansion is either a transformation of the value of parameter or information about parameter itself, depending on the value of operator.  Each operator is
                a single letter:
  
                Q      The expansion is a string that is the value of parameter quoted in a format that can be reused as input.
                E      The expansion is a string that is the value of parameter with backslash escape sequences expanded as with the $'...' quoting mechansim.
                P      The expansion is a string that is the result of expanding the value of parameter as if it were a prompt string (see PROMPTING below).
                A      The expansion is a string in the form of an assignment statement or declare command that, if evaluated, will recreate parameter with its attributes and value.
                a      The expansion is a string consisting of flag values representing parameter's attributes.
  ```
  
  参考:[Bash 4.4 中新增的 ${parameter@operator} 语法 - 紫云飞 - 博客园 (cnblogs.com)](https://www.cnblogs.com/ziyunfei/p/4918675.html)
  
  ```
  root@heimdall:~# foo=1
  root@heimdall:~# echo ${foo@Q}
  '1'
  # 原本 foo 的值只有 1 这一个字符，转换后的值有三个字符 “'1'”
  root@heimdall:~# echo ${IFS@Q}
  $' \t\n'
  # 因为 IFS 中有不可打印字符，所以转换后的值会自动使用 ANSI 转义形式的引号 $'...'，并且里面的字符也会使用反斜杠转义的形式
  root@heimdall:~# 
  
  root@heimdall:~# foo='\u4e00'
  root@heimdall:~# echo $foo
  \u4e00
  # foo 的值包含 6 个 字符，刚好是一个转义序列
  root@heimdall:~# echo ${foo@E}
  一
  # 识别并转换 foo 的值中的转义序列，就像是执行了 echo $'\u4e00' 一样
  ```

```
root@heimdall:~# foo='提示符-\s-\v\$'
root@heimdall:~# echo ${foo@P}
提示符--bash-4.4#
# \s 变成了 bash，\v 变成了 4.4，\$ 变成 $

root@heimdall:~# foo=1
root@heimdall:~# echo ${foo@A}
foo='1'
# 最普通的赋值语句
root@heimdall:~#  readonly foo
# 给 foo 加上 r 属性
root@heimdall:~#  echo ${foo@A}
declare -r foo='1'
# declare 命令的形式
root@heimdall:~#  export foo 
# 给 foo 加上 x 属性
root@heimdall:~# echo ${foo@A}
declare -rx foo='1'
# 变成了两个属性 rx


root@heimdall:~#  declare -irtu foobar=1
root@heimdall:~# echo ${foobar@a}
irtu
root@heimdall:~# 

```

除了man手册里的这5个参数，按照[Shell Parameter Expansion (Bash Reference Manual) (gnu.org)](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)

GNU官网的说明还有别的参数，但是验证的时候都不起作用
```

    root@heimdall:~# foo='提示符-\s-\v\$'
    root@heimdall:~# echo ${foo@P}
    提示符--bash-4.4#
    # \s 变成了 bash，\v 变成了 4.4，\$ 变成 $
    
    root@heimdall:~# foo=1
    root@heimdall:~# echo ${foo@A}
    foo='1'
    # 最普通的赋值语句
    root@heimdall:~#  readonly foo
    # 给 foo 加上 r 属性
    root@heimdall:~#  echo ${foo@A}
    declare -r foo='1'
    # declare 命令的形式
    root@heimdall:~#  export foo 
    # 给 foo 加上 x 属性
    root@heimdall:~# echo ${foo@A}
    declare -rx foo='1'
    # 变成了两个属性 rx
    
    
    root@heimdall:~#  declare -irtu foobar=1
    root@heimdall:~# echo ${foobar@a}
    irtu
    root@heimdall:~# 
    
```

    除了man手册里的这5个参数，按照[Shell Parameter Expansion (Bash Reference Manual) (gnu.org)](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)
    
    GNU官网的说明还有别的参数，但是验证的时候都不起作用

https://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_04.html

[Shell Parameter Expansion (Bash Reference Manual) (gnu.org)](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)



* 其他相关链接
  https://tldp.org/LDP/abs/html/https://tldp.org/LDP/abs/html/
  [README - Bash脚本进阶指南](https://linuxstory.gitbook.io/advanced-bash-scripting-guide-in-chinese/)