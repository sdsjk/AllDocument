###  ϵͳDatePickerDialog   ʹ�ó��ֵ�����

#### 1.  DatePickerDialog ��ʽ
DatePickerDialog���췽����

 ##### - 5�������Ĺ��췽��

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
 ##### - 6�������Ĺ��췽��
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

5�������Ĺ��췽�����Ҳ������6�������Ĺ��췽���������������������ǡ�0��������������6�������Ĺ��죬 

``` javascript
super(context, resolveDialogTheme(context, theme));

```
���ø���ķ������������AlertDialog�����еڶ�����������������ʽ������resolveDialogTheme��ȡ����ķ���

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

����ֻҪresid==0��ʱ�򣬻��context��������ȥ��ȡ��ʽ������ͻ�Ѵ�������resid��Ϊ����ʽȥ���á�
�����Ϊʲô����DatePickerDialog ʹ��5�������Ĺ��췽�������ڲ�ͬ�����»��в�ͬ����ʽ������ñ�����أ��Ǿ����Լ�����һ����ʽ��������Щ��ʽ���ο���https://blog.csdn.net/suwenlai/article/details/71107748
	

#### 2.  DatePickerDialog ��������ѡ����

``` javascript

 /**
     * ���ء��족
     * @param mDatePicker
     */
    private void hideDay(DatePicker mDatePicker) {
        try {
           /* ����android5.0���ϵ�������� */
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