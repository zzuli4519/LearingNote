###c++(第四单元)

###c++风格字符串

尽管c++支持c风格的字符串，但是不应该在c++程序中使用这个类型，c风格字符串往往带来许多错误，是导致大量安全问题的根源。

c++语言通过（const）char* 类型的指针来操纵c风格字符串，一般来说，使用指针的算术操作来遍历c风格字符串，每次对指针进行测试并且递增+1，直到到达结束符null为止。  

	const char *cp="this is test";	
	while(*cp){
		//dosomething to this *cp
		++cp;	
	}
while循环的条件是对const char* 类型的指针cp进行解引用，并判断cp当前指向的字符是true还是false值。true表示这是除null之外的任意字符，则继续循环直到cp指向结束字符数组的null时，循环结束。执行完之后需要将指针位置移动到下一个位置。