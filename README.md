# Changecode
import android.view.LayoutInflater
import android.view.View
import android.widget.FrameLayout
import android.widget.LinearLayout
import android.widget.TextView
import androidx.test.ext.junit.runners.AndroidJUnit4
import com.google.android.material.tabs.TabLayout
import org.junit.Assert.*
import org.junit.Before
import org.junit.Test
import org.junit.runner.RunWith
import org.mockito.Mockito.*

@RunWith(AndroidJUnit4::class)
class MainActivityTest {

    private lateinit var mainActivity: MainActivity
    private lateinit var mockInflater: LayoutInflater
    private lateinit var mockMainLayout: FrameLayout
    private lateinit var mockSeatsTabView: View
    private lateinit var mockDisabledSeatsTabView: View
    private lateinit var mockSeatTabs: TabLayout
    private lateinit var mockDisabledSeatTabs: TabLayout
    private lateinit var mockTabLayoutProvider: TabLayoutProvider

    @Before
    fun setUp() {
        mainActivity = spy(MainActivity()) // Use a spy to override specific methods if needed
        mockInflater = mock(LayoutInflater::class.java)
        mockMainLayout = mock(FrameLayout::class.java)
        mockSeatsTabView = mock(View::class.java)
        mockDisabledSeatsTabView = mock(View::class.java)
        mockSeatTabs = mock(TabLayout::class.java)
        mockDisabledSeatTabs = mock(TabLayout::class.java)
        mockTabLayoutProvider = mock(TabLayoutProvider::class.java)

        // Mock dependencies
        doReturn(mockInflater).whenever(mainActivity).getSystemService(LayoutInflater::class.java)
        doReturn(mockMainLayout).whenever(mainActivity).findViewById<FrameLayout>(R.id.frame_holder)

        // Mock inflated views
        whenever(mockInflater.inflate(eq(R.layout.seats), eq(mockMainLayout), eq(false))).thenReturn(mockSeatsTabView)
        whenever(mockInflater.inflate(eq(R.layout.seats_fnv4), eq(mockMainLayout), eq(false))).thenReturn(mockDisabledSeatsTabView)

        // Mock TabLayout
        whenever(mockSeatsTabView.findViewById<TabLayout>(R.id.tab_layout_default)).thenReturn(mockSeatTabs)
        whenever(mockDisabledSeatsTabView.findViewById<TabLayout>(R.id.tab_layout_default)).thenReturn(mockDisabledSeatTabs)

        // Mock TabLayoutProvider
        doReturn(mock(TabLayoutProvider.ToolbarLayout::class.java)).whenever(mockTabLayoutProvider).getToolbarLayout(mainActivity)

        mainActivity.tabLayoutProvider = mockTabLayoutProvider
    }

    @Test
    fun `test createSeatTabs inflates correct layouts and sets up tabs`() {
        // Arrange
        val leftSeatName = "Left Seat"
        val rightSeatName = "Right Seat"
        mainActivity.isFnv4 = false

        // Act
        mainActivity.createSeatTabs(leftSeatName, rightSeatName)

        // Assert
        // Verify correct layout inflation
        verify(mockInflater).inflate(R.layout.seats, mockMainLayout, false)
        verify(mockInflater).inflate(R.layout.seats, mockMainLayout, false)

        // Verify tab list is set up
        val iconTabListCaptor = argumentCaptor<ArrayList<TabLayout.Tab>>()
        verify(mockSeatTabs).setTabList(iconTabListCaptor.capture())
        val capturedTabs = iconTabListCaptor.firstValue
        assertEquals(2, capturedTabs.size)
        assertEquals(leftSeatName, capturedTabs[0].text)
        assertEquals(rightSeatName, capturedTabs[1].text)

        // Verify disabled tabs setup
        verify(mockDisabledSeatTabs).setTabList(iconTabListCaptor.capture())
        assertEquals(2, iconTabListCaptor.firstValue.size)
    }

    @Test
    fun `test createSeatTabs sets passenger tab click listener`() {
        // Arrange
        val leftSeatName = "Left Seat"
        val rightSeatName = "Right Seat"
        mainActivity.isFnv4 = false
        val passengerPosition = 1
        val mockTabView = mock(View::class.java)
        val mockTextView = mock(TextView::class.java)
        whenever(mockDisabledSeatTabs.getChildAt(passengerPosition)).thenReturn(mockTabView)
        doReturn(mockTextView).whenever(mainActivity).requireViewByRefId<TextView>(mockTabView, R.id.tab_text)

        // Act
        mainActivity.createSeatTabs(leftSeatName, rightSeatName)

        // Assert
        // Verify passenger tab click listener
        verify(mockTabView).setOnClickListener(any())
        // Verify textView alpha is set
        verify(mockTextView).alpha = 0.5f
    }
}
