# How to use callbacks for server sorting in Flutter DataTable (SfDataGrid)?

In this article, we will show how to use callbacks for server sorting in [Flutter DataTable](https://www.syncfusion.com/flutter-widgets/flutter-datagrid).

Initialize the [SfDataGrid](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/SfDataGrid-class.html) widget with the necessary properties. The SfDataGrid provides the following callbacks for sorting: [onColumnSortChanging](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/SfDataGrid/onColumnSortChanging.html) and [onColumnSortChanged](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/SfDataGrid/onColumnSortChanged.html). The onColumnSortChanged callback is triggered when a column is sorted in the SfDataGrid. You can use [SfDataGrid.source.sortedColumns](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/DataGridSource/sortedColumns.html) to retrieve the sorted column details, including the sort direction. Based on this direction, you can load the corresponding data into the DataGrid. After adding new data, make sure to call [notifyListeners](https://api.flutter.dev/flutter/foundation/ChangeNotifier/notifyListeners.html) to refresh the DataGrid when the underlying data changes.

Additionally, note that you should override the [performSorting](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/DataGridSource/performSorting.html) method with an empty implementation to disable built-in DataGrid sorting.

```dart
@override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Syncfusion Flutter DataGrid')),
      body: StreamBuilder(
        stream: loadingController.stream,
        builder: (BuildContext context, AsyncSnapshot<bool> snapshot) {
          return Stack(
            children: [
              SfDataGrid(
                source: employeeDataSource,
                columnWidthMode: ColumnWidthMode.fill,
                allowSorting: true,
                sortingGestureType: SortingGestureType.tap,
                onColumnSortChanged: (newSortedColumn, oldSortedColumn) async {
                  // Show loading indicator
                  loadingController.add(true);
                  await Future<void>.delayed(const Duration(seconds: 2));
                  // Hide loading indicator
                  loadingController.add(false);
                  employeeDataSource._employeeData.clear();
                  for (
                    int i = 0;
                    i < employeeDataSource.sortedColumns.length;
                    i++
                  ) {
                    if (employeeDataSource.sortedColumns[i].sortDirection ==
                        DataGridSortDirection.ascending) {
                      var employee2 = getEmployeeDataAscending();
                      employeeDataSource._buildDataGridRows(employee2);
                      employeeDataSource.updateDataGridSource();
                    } else if (employeeDataSource
                            .sortedColumns[i]
                            .sortDirection ==
                        DataGridSortDirection.descending) {
                      var employee2 = getEmployeeDataDescending();
                      employeeDataSource._buildDataGridRows(employee2);
                      employeeDataSource.updateDataGridSource();
                    }
                  }
                },
                columns: <GridColumn>[
                  GridColumn(
                    columnName: 'id',
                    label: Container(
                      padding: const EdgeInsets.all(16.0),
                      alignment: Alignment.center,
                      child: const Text('ID'),
                    ),
                  ),
                  GridColumn(
                    columnName: 'name',
                    label: Container(
                      padding: const EdgeInsets.all(8.0),
                      alignment: Alignment.center,
                      child: const Text('Name'),
                    ),
                  ),
                  GridColumn(
                    columnName: 'designation',
                    label: Container(
                      padding: const EdgeInsets.all(8.0),
                      alignment: Alignment.center,
                      child: const Text(
                        'Designation',
                        overflow: TextOverflow.ellipsis,
                      ),
                    ),
                  ),
                  GridColumn(
                    columnName: 'salary',
                    label: Container(
                      padding: const EdgeInsets.all(8.0),
                      alignment: Alignment.center,
                      child: const Text('Salary'),
                    ),
                  ),
                ],
              ),
              if (snapshot.data == true)
                const Center(child: CircularProgressIndicator()),
            ],
          );
        },
      ),
    );
  }

class EmployeeDataSource extends DataGridSource {

….


  @override
  Future<void> performSorting(List<DataGridRow> rows) async {}

  void updateDataGridSource() {
    notifyListeners();
  }
}
```

You can download this example on [GitHub](https://github.com/SyncfusionExamples/How-to-use-callbacks-for-server-sorting-in-Flutter-DataTable).