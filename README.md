import android.content.res.Resources
import androidx.lifecycle.Lifecycle
import androidx.lifecycle.ViewModelProvider
import org.junit.Before
import org.junit.Test
import org.junit.runner.RunWith
import org.mockito.Mock
import org.mockito.Mockito.*
import org.mockito.junit.MockitoJUnitRunner

@RunWith(MockitoJUnitRunner::class)
class MainActivityTest {

    @Mock
    private lateinit var mockViewModelProvider: ViewModelProvider

    @Mock
    private lateinit var mockMainActivityViewModel: MainActivityViewModel

    @Mock
    private lateinit var mockLeftSeatViewModel: LeftSeatViewModel

    @Mock
    private lateinit var mockRightSeatViewModel: RightSeatViewModel

    @Mock
    private lateinit var mockMassageLeftSeatViewModel: MassageLeftSeatViewModel

    @Mock
    private lateinit var mockMassageRightSeatViewModel: MassageRightSeatViewModel

    @Mock
    private lateinit var mockTestScreenMassageLeftSeatViewModel: TestScreenMassageLeftSeatViewModel

    @Mock
    private lateinit var mockTestScreenMassageRightSeatViewModel: TestScreenMassageRightSeatViewModel

    @Mock
    private lateinit var mockMcsTranslator: McsTranslator

    @Mock
    private lateinit var mockResponseMediator: ResponseMediator

    @Mock
    private lateinit var mockCanSignalManager: McsCanSignalManager

    @Mock
    private lateinit var mockCurrentStatusMediator: CurrentStatusMediator

    @Mock
    private lateinit var mockResources: Resources

    @Mock
    private lateinit var mockLifecycle: Lifecycle

    private lateinit var mainActivity: MainActivity

    @Before
    fun setUp() {
        mainActivity = MainActivity()

        // Mock ViewModelProvider behavior
        `when`(mockViewModelProvider.get(MainActivityViewModel::class.java)).thenReturn(mockMainActivityViewModel)
        `when`(mockViewModelProvider.get(LeftSeatViewModel::class.java)).thenReturn(mockLeftSeatViewModel)
        `when`(mockViewModelProvider.get(RightSeatViewModel::class.java)).thenReturn(mockRightSeatViewModel)
        `when`(mockViewModelProvider.get(MassageLeftSeatViewModel::class.java)).thenReturn(mockMassageLeftSeatViewModel)
        `when`(mockViewModelProvider.get(MassageRightSeatViewModel::class.java)).thenReturn(mockMassageRightSeatViewModel)
        `when`(mockViewModelProvider.get(TestScreenMassageLeftSeatViewModel::class.java)).thenReturn(mockTestScreenMassageLeftSeatViewModel)
        `when`(mockViewModelProvider.get(TestScreenMassageRightSeatViewModel::class.java)).thenReturn(mockTestScreenMassageRightSeatViewModel)
        `when`(mockViewModelProvider.get(McsTranslator::class.java)).thenReturn(mockMcsTranslator)
        `when`(mockViewModelProvider.get(ResponseMediator::class.java)).thenReturn(mockResponseMediator)

        // Mock Resource Strings
        `when`(mockResources.getString(R.string.massage_off_toast)).thenReturn("Massage Off")
        `when`(mockResources.getString(R.string.no_passenger_detected_text)).thenReturn("No Passenger Detected")

        mainActivity.resources = mockResources
        mainActivity.lifecycle = mockLifecycle
    }

    @Test
    fun `test initialSetup with normal mode`() {
        mainActivity.isTestingModeEnabled = false

        mainActivity.initialSetup()

        // Verify ViewModel assignments
        verify(mockViewModelProvider).get(MainActivityViewModel::class.java)
        verify(mockViewModelProvider).get(LeftSeatViewModel::class.java)
        verify(mockViewModelProvider).get(RightSeatViewModel::class.java)
        verify(mockViewModelProvider).get(MassageLeftSeatViewModel::class.java)
        verify(mockViewModelProvider).get(MassageRightSeatViewModel::class.java)

        // Verify correct translator and mediator setup
        verify(mockMcsTranslator).setSignalManager(any())
        verify(mockResponseMediator).setDriverLocation(any())
    }

    @Test
    fun `test initialSetup with testing mode`() {
        mainActivity.isTestingModeEnabled = true

        mainActivity.initialSetup()

        // Verify ViewModel assignments for testing mode
        verify(mockViewModelProvider).get(TestScreenMassageLeftSeatViewModel::class.java)
        verify(mockViewModelProvider).get(TestScreenMassageRightSeatViewModel::class.java)

        // Verify mediator and translator setup
        verify(mockMcsTranslator).setSignalManager(any())
        verify(mockResponseMediator).setDriverLocation(any())
    }
}