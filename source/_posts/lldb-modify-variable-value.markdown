---
layout: post
title: "XCode中使用lldb expr子命令改变变量的值"
date: 2013-08-22 00:28
comments: true
categories: [xcode,lldb]
---

## 使用场景
当调试webview中处理跳转链接的问题时，需要对特定的url链接做native页面的跳转，我需要告诉后端工程师把连接改成我要的特定url后，然后我在xcode中加断点去调试。但是这样的方法很不效率，即浪费了他的时间，也可能会影响到线上的页面。那么我可以在我的代码里加一行将url的值改为我要调试的特定格式，这样也不太好，因为我要加一行调试代码，调试完后必须要删掉，如果忘了就完蛋了，或者不小心提交svn也会对别人造成影响。无奈，最后只能借助强大的调试工具LLDB。

{% codeblock lang:objc %}

	- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:    (UIWebViewNavigationType)navigationType
	{
	
	...
	
	// 判断是否是调转到AppStore
	
	NSURL* url = request.URL;
	
	...
	
	}
{% endcodeblock %}
	
## 使用实例
在要修改变量的地方加上断点开始debug，当断点进来后，控制台里输入：


	(lldb) expr url = (NSURL*)[NSURL URLWithString:"travelguide://page/read?bookid=131"];
	 
	(NSURL *) $5 = 0x08776220 "travelguide://page/read?bookid=131"
	 
	(lldb) po url
 
	$6 = 0x08776220 travelguide://page/read?bookid=131

这样，就把变量 url的值改为新的[NSURL URLWithString:@"travelguide://page/read?bookid=131"]，既不需要修改后台页面，也不需要修改客户端代码。	

## 总结
 __expr 使用方法__


	(lldb) help expr
	Evaluate a C/ObjC/C++ expression in the current program context, using variables currently in scope. This command takes ‘raw’ input (no need to quote stuff).
	
	Syntax: expression --
	
	Command Options Usage: expression [-f ] [-G ] [-d ] [-u ] -- expression [-o] [-d ] [-u ] -- expression
	
	   -G <gdb-format>  ( --gdb-format <gdb-format> )
	        Specify a format using a GDB format specifier string.
	
	   -d <boolean>  ( --dynamic-value <boolean> )
	        Upcast the value resulting from the expression to its dynamic type
	        if available.
	
	   -f <format>  ( --format <format> )
	        Specify a format to be used for display.
	
	   -o  ( --object-description )
	        Print the object description of the value resulting from the
	        expression.
	
	   -u <boolean>  ( --unwind-on-error <boolean> )
	        Clean up program state if the expression causes a crash, breakpoint
	        hit or signal.
	Examples:
	
	expr my_struct->a = my_array[3]
	expr -f bin -- (index * 8) + 5
	expr char c[] = "foo"; c[0]
	
	IMPORTANT NOTE: Because this command takes 'raw' input, if you use any command options you must use ' -- ' between the end of the command options and the beginning of the raw input.
	
	'expr' is an abbreviation for 'expression'
	
__经验教训__

`expr`	有时不知道表达式的值类型，如常见到的错误 `no known method ; cast the message send to the method's return type`

	(lldb) expr url = [NSURL URLWithString:@"www.example.com"];
	error: no known method '+URLWithString:'; cast the message send to the method's return type
	error: 1 errors parsing expression
	
你需要强制类型转换才能达到你的期望：

	(lldb) expr url = (NSURL *)[NSURL URLWithString:@"www.example.com"];
	