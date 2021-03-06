# self made shellscripts

## git-commend.sh
### brd
Gitのローカルブランチを確認しながら一発消去するコマンド。
`if [[ $line =~ \* ]] ; then` の条件文に、 `staging` とか消されたくないブランチ名を指定したら、そもそも確認すらされずに消されなくなる。
```
brd() {
    git branch | while read line
    do
        if [[ $line =~ \* ]] ; then
            echo -e "$line : current branch\n\033[0;36m=> skip.\033[0;39m\n"
            continue
        fi
        echo -e -n "\033[0;33m$line\033[0;39m ?(yes/no) : "
        read -r ANSWER < /dev/tty
        if [ "$ANSWER" == 'yes' ] || [ "$ANSWER" == 'y' ] ; then
            git branch -D $line
            echo -e "\033[0;36mdelete success.\033[0;39m\n"
        elif [ "$ANSWER" == 'no' ] || [ "$ANSWER" == 'n' ] ; then
            echo -e "\033[0;31mnot delete.\033[0;39m\n"
        else
            echo -e "\033[0;31mdelete failure.\033[0;39m\n"
        fi
    done
}
```

### gcs
Gitのローカルブランチの切り替えをインタラクティブに行うコマンド。
オプションで番号を渡すと、その番号のブランチをチェックアウトしてくれる。
```
gcs() {
    if [ $# -eq 0 ] ; then
	git branch | while read line
	do
	    if [[ $line =~ \* ]] ; then
		echo  -e "$line : current branch\n\033[0;36m=> skip.\033[0;39m\n"
		continue
	    fi
            echo -e -n "\033[0;33m$line\033[0;39m ?(yes/no) : "
            read -r ANSWER < /dev/tty
            if [ "$ANSWER" == 'yes' ] || [ "$ANSWER" == 'y' ] ; then
		git checkout $line
		echo -e "\033[0;36mcheckout success.\033[0;39m\n"
		echo -e "\033[0;36mcurrent branch => $line\033[0;39m\n"
		break
            elif [ "$ANSWER" == 'no' ] || [ "$ANSWER" == 'n' ] ; then
		echo -e "\033[0;31mnot checkout.\033[0;39m\n"
            else
		echo -e "\033[0;31mcheckout failure.\033[0;39m\n"
            fi
	done
    else
	count=0
	git branch | while read line
	do
	    if [[ $count == $@ ]] ; then
		git checkout $line
		echo -e "\033[0;36mcheckout success.\033[0;39m\n"
		echo -e "\033[0;36mcurrent branch => $line\033[0;39m\n"
	    fi
	    count=$((++count))
	done
    fi
}
```

## Others(シェルスクリプトでなく、ただのコマンドもあり)
### my favorite terminal setting
色付けとか、二段表示とか使いやすくカスタマイズ。
```
PS1="\[\e[0;34m\]\w/\[\e[0;0m\]\n\[\e[0;36m\]> \[\e[0;0m\]"
```