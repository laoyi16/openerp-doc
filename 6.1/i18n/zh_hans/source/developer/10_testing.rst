.. i18n: =======
.. i18n: Testing
.. i18n: =======
..

=======
测试
=======

.. i18n: Unit testing
.. i18n: ============
..

单元测试
============

.. i18n: Since version 4.2 of OpenERP, the XML api provides several features to test your modules. They allow you to
..

自OpenERP 4.2版以来, 新增了利用XML文件进行模块测试的功能. 可以完成:

.. i18n:     * test the properties of your records, your class invariants etc.
.. i18n:     * test your methods
.. i18n:     * manipulate your objects to check your workflows and specific methods 
..

    * 测试记录属性，类常量等。
    * 测试您的方法
    * 操作对象，以检查流程和特殊方法

.. i18n: This thus allows you to simulate user interaction and automatically test your modules.
..

可以利用这些来模拟一个用户与系统的交互来测试你的模块.

.. i18n: Generalities
.. i18n: ------------
.. i18n:  
.. i18n: As you will see in the next pages, unit testing through OpenERP's XML can be done using three main tags: <assert>, <workflow> and <function>. All these tags share some common optional attributes:
..

概述
----

单元测试主要通过 OpenERP XML 文件的三个标签 : <assert>, <workflow> 和 <function> 来完成。其共有属性如下：

.. i18n: :uid:
..

:uid:

.. i18n: 	allows you to do the tag interpretation through a specific User ID (you must specify the XML id of that user, for example "base.user_demo") 
..

	可指定 User ID 来完成交互 (你必须指定该用户的 XML id, 如 "base.user_demo") 

.. i18n: :context:
..

:context:

.. i18n: 	allows you to specify a context dictionary (given as a Python expression) to use when applicable (for <function> notice that not all objects methods take a context attribute so it won't be automatically transmitted to them, however it applies on <value>) 
..

	可指定上下文字典（Python 表达式）内容（对于 <function> 标签需注意不是所有对象方法都会读取 context ，因此上下文不一定会传递。但对 <value> 标签可用）

.. i18n: These two attributes might be set on any of those tags (for <functions>, only the root <function> tag may accept it) or on the <data> tag itself. If you set a context attribute on both, they will be merged automatically.
..

这2个属性可设置在任意标签（对于 <functions> 标签，只有根 root <functions> 标签可用）中，或 <data> 标签本身。如果你同时设置，context 会自动合并。

.. i18n: Notice that Unit Testing tags will not be interpreted inside a <data> tag set in noupdate.
..

注意：单元测试标签在<data> 里设置为 noupdate 时，将不会被解析。

.. i18n: Using unit tests
.. i18n: ----------------
..

使用单元测试
----------------

.. i18n: You can declare unit tests in all your .XML files. We suggest you to name the files like this:
..

你可以在XML文件中声名单元测试,我们建议你如下面的样子命名：

.. i18n:     * module_name_test.xml 
..

    * module_name_test.xml 

.. i18n: If your tests are declared as demo data in the __openerp__.py, they will be checked at the installation of the system with demo data. Example of usage, testing the demo sale order produce a correct amount in the generated invoice.
..

如果你的测试在 __openerp__.py 作为演示数据申明，那么将会在系统安装演示数据时检查。例如，在生成发票过程中，测试销售订单是否产生了正确的金额。

.. i18n: If your tests are declared like init data, they will be checked at all installation of the software. Use it to test the consistency of the software after installation.
..

如果你的测试申明为初始数据（init），那么将在模块安装时检查。可以在模块安装后检查数据的一致性。

.. i18n: If your tests are declared in update sections, the tests are checked at the installation and also at all updates. Use it to tests consistencies, invariants of the module. Example: The sum of the credits must be equal to the sum of the debits for all non draft entries in the accounting module. Putting tests in update sections is very useful to check consistencies of migrations or new version upgrades. 
..

如果你的测试数据在升级部分申明，那么将在模块安装和升级时检查。用于检查数据一致性和常量。例如，会计模块中，所有非草稿状态分录总和贷方必须等于借方。将测试放在升级章节中，在模块迁移和升级中是非常有用的。

.. i18n: Assert Tag
.. i18n: ----------
..

断言标签
----------

.. i18n: The assert tag allows you to define some assertions that have to be checked at boot time. Example :
..

断言标签允许你定义某些断言，这些断言将会启动的时候检查。如：

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<assert model="res.company" id="main_company" string="The main company name is Open sprl">
.. i18n: 	    <test expr="name">Open sprl</test>
.. i18n: 	</assert>
..

.. code-block:: xml
	
	<assert model="res.company" id="main_company" string="The main company name is Open sprl">
	    <test expr="name">Open sprl</test>
	</assert>

.. i18n: This assert will check that the company with id main_company has a name equal to "Open sprl". The expr field specifies a python expression to evaluate. The expression can access any field of the specified model and any python built-in function (such as sum, reduce etc.). The ref function, which gives the database id corresponding to a specified XML id, is also available (in the case that "ref" is also the name of an attribute of the specified model, you can use _ref instead). The resulting value is then compared with the text contained in the test tag. If the assertion fails, it is logged as a message containing the value of the string attribute and the test tag that failed.
..

该断言将会检查 id 为 main_company 的 company 对象名字是否匹配 "Open sprl" 。expr 字段指定一个 python 表达式计算。该表达式可以读取指定对象的任意字段或是 python 内置函数（如sum, reduce等）。ref 函数指定数据库ID 对应的 XML id，同样也是可用（在某些例子中，ref 同时也是指定对象的属性名，这时应该使用 _ref 来代替）。经过计算后的结果值将会与 test 标签文本值比较。假如断言失败，相关属性值和测试标签信息将会作为一条消息记录下来。

.. i18n: For more complex tests it is not always sufficient to compare a result to a string. To do that you may instead omit the tag's content and just put an expression that must evaluate to True:
..

For more complex tests it is not always sufficient to compare a result to a string. To do that you may instead omit the tag's content and just put an expression that must evaluate to True:

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<assert model="res.company" 
.. i18n:                 id="main_company" 
.. i18n:                 string="The main company's currency is €" severity="warning">
.. i18n: 	    <test expr="currency_id.code == 'eur'.upper()"/>
.. i18n: 	</assert>
..

.. code-block:: xml
	
	<assert model="res.company" 
                id="main_company" 
                string="The main company's currency is €" severity="warning">
	    <test expr="currency_id.code == 'eur'.upper()"/>
	</assert>

.. i18n: The severity attribute defines the level of the assertion: debug, info, warning, error or critical. The default is error. If an assertion of too high severity fails, an exception is thrown and the parsing stops. If that happens during server initialization, the server will stop. Else the exception will be transmitted to the client. The level at which a failure will throw an exception is by default at warning, but can be specified at server launch through the ``--assert-exit-level`` argument.
..

The severity attribute defines the level of the assertion: debug, info, warning, error or critical. The default is error. If an assertion of too high severity fails, an exception is thrown and the parsing stops. If that happens during server initialization, the server will stop. Else the exception will be transmitted to the client. The level at which a failure will throw an exception is by default at warning, but can be specified at server launch through the ``--assert-exit-level`` argument.

.. i18n: As sometimes you do not know the id when you're writing the test, you can use a search instead. So we can define another example, which will be always true:
..

As sometimes you do not know the id when you're writing the test, you can use a search instead. So we can define another example, which will be always true:

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<assert model="res.partner" 
.. i18n:                 search="[('name','=','Agrolait')]" 
.. i18n:                 string="The name of Agrolait is :Agrolait">
.. i18n: 	    <test expr="name">Agrolait</test>
.. i18n: 	</assert>
..

.. code-block:: xml
	
	<assert model="res.partner" 
                search="[('name','=','Agrolait')]" 
                string="The name of Agrolait is :Agrolait">
	    <test expr="name">Agrolait</test>
	</assert>

.. i18n: When you use the search, each resulting record is tested but the assertion is counted only once. Thus if an assertion fails, the remaining records won't be tested. In addition, if the search finds no record, nothing will be tested so the assertion will be considered successful. If you want to make sure that there are a certain number of results, you might use the count parameter:
..

When you use the search, each resulting record is tested but the assertion is counted only once. Thus if an assertion fails, the remaining records won't be tested. In addition, if the search finds no record, nothing will be tested so the assertion will be considered successful. If you want to make sure that there are a certain number of results, you might use the count parameter:

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<assert model="res.partner" 
.. i18n:                 search="[('name','=','Agrolait')]" 
.. i18n:                 string="The name of Agrolait is :Agrolait" 
.. i18n:                 count="1">
.. i18n: 	    <test expr="name">Agrolait</test>
.. i18n: 	</assert>
..

.. code-block:: xml
	
	<assert model="res.partner" 
                search="[('name','=','Agrolait')]" 
                string="The name of Agrolait is :Agrolait" 
                count="1">
	    <test expr="name">Agrolait</test>
	</assert>

.. i18n: :Example:
..

:Example:

.. i18n: Require the version of a module.
..

Require the version of a module.

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<!-- modules requirement -->
.. i18n: 	<assert model="ir.module.module" 
.. i18n:                 search="[('name','=','common')]" 
.. i18n:                 severity="critical" count="1">
.. i18n: 	    <test expr="state == 'installed'" />
.. i18n: 	    <!-- only check module version -->
.. i18n: 	    <test expr="'.'.join(installed_version.split('.')[3:]) >= '2.4'" />
.. i18n: 	</assert>
.. i18n: 	
.. i18n: 	
.. i18n: Workflow Tag
.. i18n: ------------
..

.. code-block:: xml
	
	<!-- modules requirement -->
	<assert model="ir.module.module" 
                search="[('name','=','common')]" 
                severity="critical" count="1">
	    <test expr="state == 'installed'" />
	    <!-- only check module version -->
	    <test expr="'.'.join(installed_version.split('.')[3:]) >= '2.4'" />
	</assert>
	
	
工作流标签
------------

.. i18n: The workflow tag allows you to call for a transition in a workflow by sending a signal to it. It is generally used to simulate an interaction with a user (clicking on a button…) for test purposes:
..

The workflow tag allows you to call for a transition in a workflow by sending a signal to it. It is generally used to simulate an interaction with a user (clicking on a button…) for test purposes:

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<workflow model="sale.order" ref="test_order_1" action="order_confirm" />
..

.. code-block:: xml
	
	<workflow model="sale.order" ref="test_order_1" action="order_confirm" />

.. i18n: This is the syntax to send the signal ``order_confirm`` to the sale order with id ``test_order_1``.
..

This is the syntax to send the signal ``order_confirm`` to the sale order with id ``test_order_1``.

.. i18n: Notice that workflow tags (as all other tags) are interpreted as root which might be a problem if the signals handling needs to use some particular property of the user (typically the user's company, while root does not belong to one). In that case you might specify a user to switch to before handling the signal, through the uid property:
..

Notice that workflow tags (as all other tags) are interpreted as root which might be a problem if the signals handling needs to use some particular property of the user (typically the user's company, while root does not belong to one). In that case you might specify a user to switch to before handling the signal, through the uid property:

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<workflow model="sale.order" ref="test_order_1" action="manual_invoice" uid="base.user_admin" />
..

.. code-block:: xml
	
	<workflow model="sale.order" ref="test_order_1" action="manual_invoice" uid="base.user_admin" />

.. i18n: (here we had to specify the module base - from which user_admin comes - because this tag is supposed to be placed in an xml file of the sale module)
..

(here we had to specify the module base - from which user_admin comes - because this tag is supposed to be placed in an xml file of the sale module)

.. i18n: In some particular cases, when you write the test, you don't know the id of the object to manipulate through the workflow. It is thus allowed to replace the ref attribute with a value child tag:
..

In some particular cases, when you write the test, you don't know the id of the object to manipulate through the workflow. It is thus allowed to replace the ref attribute with a value child tag:

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<workflow model="account.invoice" action="invoice_open">
.. i18n: 	    <value model="sale.order" eval="obj(ref('test_order_1')).invoice_ids[0].id" />
.. i18n: 	</workflow>
..

.. code-block:: xml
	
	<workflow model="account.invoice" action="invoice_open">
	    <value model="sale.order" eval="obj(ref('test_order_1')).invoice_ids[0].id" />
	</workflow>

.. i18n: (notice that the eval part must evaluate to a valid database id) 
..

(notice that the eval part must evaluate to a valid database id) 

.. i18n: Function Tag
.. i18n: ------------
..

函数标签
------------

.. i18n: The function tag allows to call some method of an object. The called method must have the following signature:
..

The function tag allows to call some method of an object. The called method must have the following signature:

.. i18n: def mymethod(self, cr, uid [, …])
..

def mymethod(self, cr, uid [, …])

.. i18n: Where
..

Where

.. i18n:     * cr is the database cursor
.. i18n:     * uid is the user id 
..

    * cr is the database cursor
    * uid is the user id 

.. i18n: Most of the methods defined in Tiny respect that signature as cr and uid are required for a lot of operations, including database access.
..

Most of the methods defined in Tiny respect that signature as cr and uid are required for a lot of operations, including database access.

.. i18n: The function tag can then be used to call that method:
..

The function tag can then be used to call that method:

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<function model="mypackage.myclass" name="mymethod" />
..

.. code-block:: xml
	
	<function model="mypackage.myclass" name="mymethod" />

.. i18n: Most of the time you will want to call your method with additional arguments. Suppose the method has the following signature:
..

Most of the time you will want to call your method with additional arguments. Suppose the method has the following signature:

.. i18n: def mymethod(self, cr, uid, mynumber)
..

def mymethod(self, cr, uid, mynumber)

.. i18n: There are two ways to call that method:
..

There are two ways to call that method:

.. i18n:     * either by using the eval attribute, which must be a python expression evaluating to the list of additional arguments: 
..

    * either by using the eval attribute, which must be a python expression evaluating to the list of additional arguments: 

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<function model="mypackage.myclass" name="mymethod" eval="[42]" />
..

.. code-block:: xml
	
	<function model="mypackage.myclass" name="mymethod" eval="[42]" />

.. i18n: In that case you have access to all native python functions, to a function ``ref()`` that takes as its argument an XML id and returns the corresponding database id, and to a function ``obj()`` that takes a database id and returns an object with all fields loaded as well as related records.
..

In that case you have access to all native python functions, to a function ``ref()`` that takes as its argument an XML id and returns the corresponding database id, and to a function ``obj()`` that takes a database id and returns an object with all fields loaded as well as related records.

.. i18n:     * or by putting a child node inside the function tag: 
..

    * or by putting a child node inside the function tag: 

.. i18n: .. code-block:: xml
.. i18n: 	
.. i18n: 	<function model="mypackage.myclass" name="mymethod">
.. i18n: 	     <value eval="42" />
.. i18n: 	</function>
..

.. code-block:: xml
	
	<function model="mypackage.myclass" name="mymethod">
	     <value eval="42" />
	</function>

.. i18n: Only value and function tags have meaning as function child nodes (using other tags will give unspecified results). This means that you can use the returned result of a method call as an argument of another call. You can put as many child nodes as you want, each one being an argument of the method call (keeping them in order). You can also mix child nodes and the eval attribute. In that case the attribute will be evaluated first and child nodes will be appended to the resulting list. 
..

Only value and function tags have meaning as function child nodes (using other tags will give unspecified results). This means that you can use the returned result of a method call as an argument of another call. You can put as many child nodes as you want, each one being an argument of the method call (keeping them in order). You can also mix child nodes and the eval attribute. In that case the attribute will be evaluated first and child nodes will be appended to the resulting list. 

.. i18n: Acceptance testing
.. i18n: ==================
..

验收测试
==================

.. i18n: This document describes all tests that are made each time someone install OpenERP on a computer. You can then assume that all these tests are valid as we must launch them before publishing a new module or a release of OpenERP.
..

这个文档描述的是所有人在计算机上安装OpenERP的测试。你可以假设所有测试都已通过致使我们必须更新整合到发行版的新模块中或者一个新OpenERP候选版本中。

.. i18n: Integrity tests on migrations
.. i18n: -----------------------------
..

迁移完整性测试
-----------------------------

.. i18n:             * Sum credit = Sum debit
.. i18n:             * Balanced account chart 
..

            * Sum credit = Sum debit
            * Balanced account chart 

.. i18n: ... Describe all integrity tests here
..

... Describe all integrity tests here

.. i18n: Workflow tests
.. i18n: --------------
..

工作流测试
--------------

.. i18n: ... Describe all processes tested here.
..

... Describe all processes tested here.

.. i18n: Record creation
.. i18n: ---------------
..

记录生成
---------------

.. i18n: More than 300 records are created, describe them here. 
..

More than 300 records are created, describe them here. 
