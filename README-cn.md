# ZAdapter

# 优点

-[x] recyclerView链式调用
-[x] 可复用,资源id写在复用类里面
-[x] Helper类的链式调用与可扩展
-[x] 空数据view的支持

>为什么使用 helper 而不是 bindview?
> 原因： 可定制封装逻辑，不强转，可连写；

# Usage

### JicPack
Add it in your root build.gradle at the end of repositories:

	allprojects {
		repositories {
			...
			maven { url "https://jitpack.io" }
		}
	}
Step 2. Add the dependency
> compile 'com.github.luhaoaimama1:ZAdapter3:[Latest release](https://github.com/luhaoaimama1/ZAdapter3/releases)'
    

# Easy use:

1.简单使用

```
    IAdapter<String> muliAdapter = new QuickRcvAdapter(this, mDatas){
                @Override
                protected int getItemViewType2(int dataPosition) {
                //此方法可以默认 设置不写, 默认值返回Wrapper.DEFAULT_VALUE(-1)
                    return dataPosition % 2;
                }
            };
    muliAdapter
                .addViewHolder(new LeftDelegates())//默认
                .addViewHolder(0, new LeftDelegates()) //多部剧 注释开启即可
                .addViewHolder(1, new RightDelegates())//多部剧 注释开启即可
                .addHeaderHolder(R.layout.header_simple)//资源
                .addHeaderHolder(ViewDelegates footer)//也可以的
                .addFooterHolder(R.layout.footer_simple)//资源
                .addFooterHolder(ViewDelegates footer)//也可以的
                .addEmptyHold(R.layout.empty)//也支持empty 资源
                .addEmptyHold(ViewDelegates emtpy)//也支持empty
                .setOnItemClickListener(new IAdapter.OnItemClickListener() {
                    @Override
                    public void onItemClick(ViewGroup parent, View view, int position) {
                        System.out.println("被点击->onItemClick" + position);
                    }
                })
                .setOnItemLongClickListener(new IAdapter.OnItemLongClickListener() {
                    @Override
                    public boolean onItemLongClick(ViewGroup parent, View view, int position) {
                        System.out.println("被点击->onItemLongClick:" + position);
                        return true;
                    }
                })
                .addOnScrollListener(scroller = new OnScrollRcvListenerEx(new OnScrollRcvListenerEx.LoadMoreCallback() {
                    @Override
                    public void loadMore() {
                    //这个类主要是为了和我的ZRefresh库兼容。我会委托上啦加载在这里处理!
                    }
                }))
                .relatedList(rv)
```

2.ViewDelegates的使用与

```
public class LeftDelegates extends ViewDelegates<String> {

    @Override
    public int getLayoutId() {
        return R.layout.item_left;
    }

    @Override
    public void fillData(int postion, String data, Helper helper) {
//        helper.setText(R.id.tv, data);

        ExtraHelper.wrapper(helper).setText(R.id.tv, data).heihei().heihei2()
                .setOnClickListener(new View.OnClickListener() {
                    @Override
                    public void onClick(View view) {
                        System.out.println("helper click测试 ");
                    }
                }, new int[]{R.id.tv, R.id.ll_main});
        ;
    }
}

```

3.Helper的扩展技巧:扩展技巧是 装饰模式+链式调用

```
public class ExtraHelper extends Helper<ExtraHelper> {


    private ExtraHelper(Helper helper) {
        super(helper.getContext(), helper.getHolder(),helper.getAdapter());
        child = this;
    }

    public static ExtraHelper wrapper(Helper helper){
        return new ExtraHelper(helper);
    }

    public ExtraHelper heihei() {
        System.out.println("heihei!");
        checkChild();
        return child;
    }

    public ExtraHelper heihei2() {
        System.out.println("heihei2!");
        checkChild();
        return child;
    }
}

```

4.全局配置

    //全局替换加载更多
    QuickConfig.build().setLoadMoreDelegates(ILoadMoreDelegates iLoadMoreDelegates).perform();


# Reference&Thanks：
https://github.com/JoanZapata/base-adapter-helper

https://github.com/cundong/HeaderAndFooterRecyclerView

https://github.com/GcsSloop/diycode