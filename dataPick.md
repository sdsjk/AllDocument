###  系统DatePickerDialog   使用出现的问题

#### 1.  DatePickerDialog 样式
DatePickerDialog构造方法中

 ##### - 5个参数的构造方法

```java
/**
     * @param context The context the dialog is to run in.
     * @param callBack How the parent is notified that the date is set.
     * @param year The initial year of the dialog.
     * @param monthOfYear The initial month of the dialog.
     * @param dayOfMonth The initial day of the dialog.
     */
    public DatePickerDialog(Context context,
            OnDateSetListener callBack,
            int year,
            int monthOfYear,
            int dayOfMonth) {
        this(context, 0, callBack, year, monthOfYear, dayOfMonth);
    }
```
 ##### - 6个参数的构造方法
```java
public DatePickerDialog(Context context, int theme, OnDateSetListener listener, int year,
            int monthOfYear, int dayOfMonth) {
        super(context, resolveDialogTheme(context, theme));

        mDateSetListener = listener;
        mCalendar = Calendar.getInstance();

        final Context themeContext = getContext();
        final LayoutInflater inflater = LayoutInflater.from(themeContext);
        final View view = inflater.inflate(R.layout.date_picker_dialog, null);
        setView(view);
        setButton(BUTTON_POSITIVE, themeContext.getString(R.string.ok), this);
        setButton(BUTTON_NEGATIVE, themeContext.getString(R.string.cancel), this);
        setButtonPanelLayoutHint(LAYOUT_HINT_SIDE);

        mDatePicker = (DatePicker) view.findViewById(R.id.datePicker);
        mDatePicker.init(year, monthOfYear, dayOfMonth, this);
        mDatePicker.setValidationCallback(mValidationCallback);
    }
```

5个参数的构造方法最后也调用了6个参数的构造方法，其中主题参数传入的是“0”，接下来看下6个参数的构造， 

``` javascript
super(context, resolveDialogTheme(context, theme));

```
调用父类的方法，父类就是AlertDialog，其中第二个参数就是主题样式，看下resolveDialogTheme获取主题的方法

``` javascript
 static int resolveDialogTheme(Context context, int resid) {
        if (resid == 0) {
            final TypedValue outValue = new TypedValue();
            context.getTheme().resolveAttribute(R.attr.datePickerDialogTheme, outValue, true);
            return outValue.resourceId;
        } else {
            return resid;
        }
    } 
	
```

发现只要resid==0的时候，会从context上下文中去获取样式，否则就会把传进来的resid作为的样式去设置。
这就是为什么创建DatePickerDialog 使用5个参数的构造方法，会在不同环境下会有不同的样式，如何让避免的呢，那就是自己传入一个样式，都有那些样式，参考：https://blog.csdn.net/suwenlai/article/details/71107748
	

#### 2.  DatePickerDialog 隐藏日子选择器

``` javascript

 /**
     * 隐藏“天”
     * @param mDatePicker
     */
    private void hideDay(DatePicker mDatePicker) {
        try {
           /* 处理android5.0以上的特殊情况 */
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                int daySpinnerId = Resources.getSystem().getIdentifier("day", "id", "android");
                if (daySpinnerId != 0) {
                    View daySpinner = mDatePicker.findViewById(daySpinnerId);
                    if (daySpinner != null) {
                        daySpinner.setVisibility(View.GONE);
                    }
                }
            } else {
                Field[] datePickerfFields = mDatePicker.getClass().getDeclaredFields();
                for (Field datePickerField : datePickerfFields) {
                    if ("mDaySpinner".equals(datePickerField.getName()) || ("mDayPicker").equals(datePickerField.getName())) {
                        datePickerField.setAccessible(true);
                        Object dayPicker = new Object();
                        try {
                            dayPicker = datePickerField.get(mDatePicker);
                        } catch (IllegalAccessException e) {
                            e.printStackTrace();
                        } catch (IllegalArgumentException e) {
                            e.printStackTrace();
                        }
                        ((View) dayPicker).setVisibility(View.GONE);
                    }
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

```