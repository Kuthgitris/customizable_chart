<!--
This README describes the package. If you publish this package to pub.dev,
this README's contents appear on the landing page for your package.

For information about how to write a good package README, see the guide for
[writing package pages](https://dart.dev/guides/libraries/writing-package-pages).

For general information about developing packages, see the Dart guide for
[creating packages](https://dart.dev/guides/libraries/create-library-packages)
and the Flutter guide for
[developing packages and plugins](https://flutter.dev/developing-packages).
-->

It's a widget that allows you to design your bars with your own data structures and a lot of details.

![ghi2](https://github.com/00Kerem00/customizable_chart/assets/143900054/66fcb698-e104-4179-98d4-ab32c0379b1e)
![ghi3](https://github.com/00Kerem00/customizable_chart/assets/143900054/ef36b4fb-8175-4476-9eb3-283b7d8c3b48)

It gives you a chart with rects and draws your bar design in those rects.

![ghi0](https://github.com/00Kerem00/customizable_chart/assets/143900054/9337d6de-0cc7-4c41-9553-a7709256fcea)
![ghi1](https://github.com/00Kerem00/customizable_chart/assets/143900054/76e71ad5-8e66-4c08-be66-f07fcb4e6134)

## Before started

You need to have knowledge of CustomPaint and ChangeNotifiers for use this.

## Usage

Let's start with add dependency to "pubspec.yaml".

```yaml
dependencies:
  customizable_chart:
    git:
      url: "https://github.com/00Kerem00/customizable_chart.git"
      ref: master
```


Then create a dart file named as "sample_chart" has the widget that will build our designed chart. And import 'package:customizable_chart/customizable_chart.dart'.
We have 3 parent classes to design our chart. BarData, BarPainter, BarGroupDrawer.


### BarData
In BarData we store height reference values, labels, priority and other values. And add data we want to display in a list of BarData

| Properties | Description        | Type |
|------------|--------------------|-----|
| `heightReferenceValues` | Values that you want to show as heights in this chart. | Map<String, double> |
| `label` | Name of the value. This will appear bottom of bar. | String |
| `labelPriority` | When the chart zoom out some of your bar labels are not able to appear. They appear according to a period. If this period doesn't allow any high priority label to write it, writes above next label. | int |
| `minValue, maxValue` | Min and max values of heightReferenceValues for define indicator state. They determines in constructor. |     |


### BarPainter
We design our bars in this class with CustomPainter principles.

| Methods    | Description                | Type |
|------------|-------------------------|------|
| `paintBar(Canvas canvas, Size size, Offset offset, BarData barData, Map<String, double> heights)`| This is the method you will override and add your own data drawings. Heights are determined by BarGroupDrawer from reference values according to other bars that currently appearing in chart      | void |


### BarGroupDrawer
If we want to draw anything without depending on bars, we can add some extras.
![ghi4](https://github.com/00Kerem00/customizable_chart/assets/143900054/eb8355c8-15fe-4134-bda8-a4a3f7544f65)

| Properties   | Description                                                                  | Type          |
|--------------|------------------------------------------------------------------------------|---------------|
| `barData`    | The whole data that you want to show in this chart.                          | List<BarData> |
| `barPainter` |                                                                              | BarPainter    |
| `minValue, maxValue` | Min and Max values of heightReferenceValues in all currently appearing bars. | double     |
| Methods      |                                                                              |               |
| `void drawBars(Canvas canvas, List<Rect> rects, List<int> indexesOfDisplayed, double unitScale, double minValue, double maxValue)`  |                                                                              | void          |


We need to define 3 classes that extends these classes.

```dart
class SampleBarData extends BarData
{
  // Actually if we want to make just a simple chart that contains one height reference value, we don't need to add extra properties.
  // But if we want to show different values like color or shape we can make custom bar data. If you want to see sample of custom bar data check out SuperGraphic in examples.
}

class SampleBarPainter extends BarPainter
{
  final gradient = const LinearGradient(
      colors: [Color.fromARGB(255, 229, 210, 209), Color.fromARGB(255, 193, 136, 135), Color.fromARGB(255, 66, 28, 28)],
      stops:  [0.25, 0.5, 1.0],
      begin: Alignment(0, 1), end: Alignment(0, -1)
  );																// Gradient color of bars.

  @override
  void paintBar(Canvas canvas, Size size, Offset offset, BarData barData, Map<String, double> heights)				// This method gives the rect information as size and offset that we want to draw our bar inside of it
  {
    Rect bar = Rect.fromLTRB(offset.dx, heights["value_0"], offset.dx + size.width, size.height);
    final paint = Paint()..shader = gradient.createShader(gradient)..style = PaintingStyle.fill;

    canvas.drawRect(openClose, paint);
  }
}

class SampleBarGroupDrawer extends BarGroupDrawer
{
  SampleBarGroupDrawer(super.barData, super.barPainter);

  @override
  drawBars(Canvas canvas, List<Rect> rects, List<int> indexesOfDisplayed, double unitScale, double minValue, double maxValue)
  {
    super.minValue = minValue; super.maxValue = maxValue;			// Attention: this assignment is necessary to use getHeightsFromReferenceValues()

    for(int i = 0; i < indexesOfDisplayed; i++)
    {
      BarData data = barData[indexesOfDisplayed[i]];
      barPainter.paintBar(canvas, rects[i], rects[i].topLeft, data, getHeightsFromReferenceValues(data.heightReferenceValues, unitScale));
    }
  }

}
```


Now we will define the stateless widget class that returns our chart.

```dart
class SampleGraphic extends StatelessWidget
{
	SampleGraphic(this.Size)
	Size size;

	@override
  	Widget build(BuildContext context)
	{
		return CustomizableChart(size: size, barGroupDrawer: SampleBarGroupDrawer(exampleData(), SampleBarPainter()));
	}

	List<SampleBarData> exampleData()
	{
		List<SampleBarData> result = [];
		result.add({"value_0": 8218.47});
		result.add({"value_0": 8291.55});
		result.add({"value_0": 8161.14});
		result.add({"value_0": 8141.33});
		result.add({"value_0": 7966.65});
		result.add({"value_0": 7964.48});
		result.add({"value_0": 7963.44});
		result.add({"value_0": 7837.07});
		result.add({"value_0": 7545.17});
		result.add({"value_0": 7648.30});
		result.add({"value_0": 7829.40});
		result.add({"value_0": 8218.47});
		result.add({"value_0": 8291.55});
		result.add({"value_0": 8161.14});
		result.add({"value_0": 8141.33});
		result.add({"value_0": 7966.65});
		result.add({"value_0": 7964.48});
		result.add({"value_0": 7963.44});
		result.add({"value_0": 7837.07});
		result.add({"value_0": 7545.17});
		result.add({"value_0": 7648.30});
		result.add({"value_0": 7829.40});

		return result;
	}
}
```
