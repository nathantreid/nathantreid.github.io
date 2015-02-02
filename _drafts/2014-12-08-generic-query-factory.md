    public class QueryFactory<T, I> : IQueryFactory<I> where T : class, I
    {
        private readonly IDatabaseFactory databaseFactory;

        public QueryFactory(IDatabaseFactory databaseFactory)
        {
            this.databaseFactory = databaseFactory;
        }

        public I Get(IDatabase database = null)
        {
            return (I)Activator.CreateInstance(typeof(T), new object[] { database ?? databaseFactory.Get() });
        }
    }


    class Query_Factory
    {
        [Test]
        public void Should_create_query_of_supplied_type()
        {
            var numberOfRuns = 100000;
            var genericFactoryStopwatch = new Stopwatch();
            var specificFactoryStopwatch = new Stopwatch();
            var fastGenericFactoryStopwatch = new Stopwatch();
            genericFactoryStopwatch.Start();
            var databaseFactory = Mock.Of<IDatabaseFactory>();
            for (var i = 0; i < numberOfRuns; i++)
            {
                var factory = new QueryFactory<TestQuery, ITestQuery>(databaseFactory);
                ITestQuery query = factory.Get();
            }
            genericFactoryStopwatch.Stop();
            fastGenericFactoryStopwatch.Start();
            for (var i = 0; i < numberOfRuns; i++)
            {
                var factory = new FastQueryFactory<TestQuery, ITestQuery>(databaseFactory);
                ITestQuery query = factory.Get();
            }

            fastGenericFactoryStopwatch.Stop();
            specificFactoryStopwatch.Start();
            for (var i = 0; i < numberOfRuns; i++)
            {
                var factory = new TestQueryFactory(databaseFactory);
                ITestQuery query = factory.Get();
            }
            specificFactoryStopwatch.Stop();
            Console.WriteLine(Smart.Format("Number of runs: {numberOfRuns:#,###}", new { numberOfRuns }));
            LogResult("Generic", genericFactoryStopwatch, numberOfRuns);
            LogResult("Fast", fastGenericFactoryStopwatch, numberOfRuns);
            LogResult("Specific", specificFactoryStopwatch, numberOfRuns);
        }

        public void LogResult(string type, Stopwatch stopwatch, int numberOfRuns)
        {
            const string stopwatchLogTemplate = "{type}: {elapsedSeconds} seconds; Avg: {instanceAverage} ms";

            Console.WriteLine(Smart.Format(stopwatchLogTemplate, new { type, elapsedSeconds = stopwatch.Elapsed.TotalSeconds, instanceAverage = stopwatch.ElapsedMilliseconds / (float)numberOfRuns }));
        }
    }

    public class TestQueryFactory : IQueryFactory<ITestQuery>
    {
        private readonly IDatabaseFactory databaseFactory;

        public TestQueryFactory(IDatabaseFactory databaseFactory)
        {
            this.databaseFactory = databaseFactory;
        }

        public ITestQuery Get(IDatabase database = null)
        {
            return new TestQuery(database ?? databaseFactory.Get());
        }

    }
    internal class TestQuery : ITestQuery
    {
        public TestQuery(IDatabase database)
        {

        }
    }

    public interface ITestQuery
    {
    }



Number of runs: 100,000
Generic: 1.1045662 seconds; Avg: 0.01104 ms
Fast: 0.2929315 seconds; Avg: 0.00292 ms
Specific: 0.2147584 seconds; Avg: 0.00214 ms
